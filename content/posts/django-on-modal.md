---
title: "Deploying AI-powered Django apps to Modal.com"
date: "2023-12-29"
summary: "Modal is a great place to deploy Python apps that use AI models and require GPU. Here's how to use Modal with Django apps."
tags:
  - projects
  - python
  - django
  - ai
---

I found a great place to deploy my AI projects — [Modal.com](https://www.notion.so/Deploying-Django-apps-to-Modal-com-835da6cd51c042b792b3e65136699c17?pvs=21). It’s nice because it provides access to GPU-powered hosting for a hourly price, and it if nobody is using my app, I’m not paying for the hosting. They give $30/mo in credits, which is more than enough for me.

This is a much better deal than getting a $7/mo Render or Heroku with 512MB of RAM that can’t even load pytorch models I’m using into memory without crashing.

My recent AI TODO project is built using [Django](https://www.djangoproject.com/). Deploying it to Modal was a bit tricky, so I decided to write this blog post about my experience.

In theory, Modal supports [WSGI-compatible](https://modal.com/docs/guide/webhooks#serving-asgi-and-wsgi-apps) apps and Django exports [WSGI](https://docs.djangoproject.com/en/5.0/howto/deployment/wsgi/)-compatible module. So the simplest implementation could looks like this:

---

```python
@stub.function()
@wsgi_app()
def run():
    from todoApp.wsgi import application
        return application
```

---

However, I found a few issues that I had to work around:

1. By default, Modal doesn’t copy all my project’s files into the container and the app crashes when trying to render the missing html templates. So I had to add a few `copy_local_dir` instructions. `copy_local_dir('.')` was a bad idea because it pulled too much data (e.g. .git repo, sqlite db, etc.)
2. During the build, I wanted it to apply migrations to the database via `manage.py migrate`. However, the code requires some environment variables to be present during the build phase, so I needed to use `secrets` to make sure they are present.
3. The cold start of the app was slow and sometimes it spawned more than one app instance. Most of my AI apps are meant as demos I can share with friends and colleagues, so I adjusted the concurrency and idle timeout. I’ve also decided to stick with a local SQLite database, this way when the container goes back to sleep, all data is removed. When I decide I need the data to be persisted, I’ll look into using a hosted DB like Postgres.

The final `modal_app.py` looks like this:

---

```python
import os
from modal import Image, Secret, Stub, wsgi_app

image = (
    Image.debian_slim(python_version="3.11")
        .pip_install_from_requirements("requirements.txt")
        .copy_local_dir('staticfiles', '/root/staticfiles')
        .copy_local_dir('todoApp', '/root/todoApp')
        .copy_local_dir('todos', '/root/todos')
        .copy_local_file('manage.py', '/root/manage.py')
        .run_commands(
            "python /root/manage.py migrate",
            secrets=[Secret.from_name("my-app-secret")],
        )
)

stub = Stub(name="my-app", image=image)

@stub.function(
        secret=Secret.from_name("my-app-secret"),
        gpu="T4",
        concurrency_limit=1,
        container_idle_timeout=300,
    )
@wsgi_app()
def run():
    from todoApp.wsgi import application
        return application
```

---

And to run and deploy I use:

```bash
$ python -m modal deploy modal_app.py
```

After successful deployment I get a URL I can send to anyone. If the container is not running, it might take 5-15 seconds to boot up. I can also see the logs and metrics in the Modal console:

![Console](/images/modal_command.png)

My next steps:

1. Add this as GitHub action so that every time I push my code to GitHub, the app gets deployed to Modal
2. Use my custom domain name for the web endpoints
