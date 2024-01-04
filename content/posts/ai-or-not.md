---
title: "AI or Not — detect AI-generated photos using AI"
date: "2023-12-19"
summary: "I built an app that can tell apart AI-generated pictures from real photos. It can easily detect outputs from Midjourney v6 or SDXL. Try it here."
tags:
  - projects
  - python
  - ai
  - fastai
  - pytorch
  - gradio
---

_Try the classifier at [ivan-tolkunov--fake-or-not-run.modal.run](https://ivan-tolkunov--fake-or-not-run.modal.run/) (warning: the app could take up to 30s to boot up)._

![](/images/ai_or_not_ui.png)

The new AI models for image generations, like Stable Diffusion XL or Midjourney v6, are getting really good at making photorealistic images. Sometimes it's hard to tell if the images are real even when looking at them for a while.

I wanted to see if it's possible to train a classifier that's able to detect AI-generated photos.

To make iteration faster, I managed to [configure PyTorch to use my GPU on my M2 MacBook Air](https://pytorch.org/docs/stable/notes/mps.html). It made things a bit faster, but I wish I had a better GPU, since so many things in AI development are optimized for CUDA.

```python
import torch
from fastai.vision.all import *

print(torch.backends.mps.is_available())
default_device(torch.device("mps"))
```

I used a simple resnet-based classifier model and the FastAI framework for training on a dataset of images. After playing with parameters a bit, I found that `resnet18` is enough to get 99%+ accurancy.

```python
dls = DataBlock(
    blocks=(ImageBlock, CategoryBlock),
    get_items=get_image_files,
    splitter=RandomSplitter(valid_pct=0.2, seed=42),
    get_y=parent_label
).dataloaders(self.path, bs=32)

learn = vision_learner(dls, resnet18, metrics=error_rate)
learn.fine_tune(5)

learn.export('./models/realOrFake_resnet18_224px.pkl')
```

![](/images/confusion_matrix.jpg)

To allow users to test their own images, I made a simple web app. This time I used [Gradio](https://www.gradio.app/), a Python web app framework that makes it super easy to build simple interfaces that actually look nice.

```python
import gradio as gr
from gradio.routes import mount_gradio_app

mount_gradio_app(
    app=web_app,
    blocks=gr.Interface(fn=predict,
            inputs=gr.Image(type="pil"),
            outputs=gr.Label(num_top_classes=2),
            examples=[
                # list of example URLs
            ]),
    path="/",
)
```

When the user submits an image, our `predict` function gets executed on the backend. It returns a list of labels and probabilities. Good news is that FastAI classifiers already return data in the right format, I only needed to convert tensors to floats:

```python
def predict(fake_or_real):
    model = load_learner('/models/realOrFake_resnet18_224px.pkl')
    model.eval()

    result ,_ , probs = model.predict(fake_or_real)
    print(result, probs)
    return {'ai': float(probs[0]), 'real': float(probs[1])}
```

Full source code is available at https://github.com/ivan-tolkunov/fake_or_not
