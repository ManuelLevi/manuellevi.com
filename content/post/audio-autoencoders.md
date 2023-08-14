+++
author = "Manuel Levi"
date = 2022-06-20T19:06:23Z
description = ""
draft = true
slug = "audio-autoencoders"
title = "Audio autoencoders"

+++




## Raw audio is difficult

**and heavy.**

Typically when we talk about audio files, most use a sampling frequency between 44.1kHz and 48kHz. That means that for a single second, there are 44100 data points, typically.

Knowing that Long Short-Term Memory (LSTM) networks can hardly learn patterns in sequences of up a few hundred, this poses a channel.

Not only that but to identify long-range dependencies like pitch, melody, timbre, structure, rhythm, and harmony you would need an exceedingly complex model, which would take an excessive amount of time to train, and an increased risk of your model not training correctly (vanishing gradients, and so on). Not only that but because of the sheer number of parameters, these networks are typically slow AF.

{{< figure src="__GHOST_URL__/content/images/2022/06/CiJ5YGUWUAAFaLT.jpg" caption="Raw audio vs your model" >}}

## A better representation for sound

Spectrograms: Take a waveform, and transform it into the time-frequency domain (Time vs Frequency).

<iframe width="200" height="113" src="https://www.youtube.com/embed/wSYAZnQmffg?feature=oembed" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen title="[Spectrogram] Aphex Twin / ΔMi−1 = −∂Σn=1NDi[n][Σj∈C{i}Fji[n − 1] + Fexti[[n−1]]"></iframe>

Humans process sound in interesting ways. We don't perceive volume or frequencies linearly (same for light). Because of that, Mel spectrograms were created. Mel spectrograms plot the frequencies on a different scale, the mel scale. (no surprise there!). By applying this non-linear transformation, you get a spectrogram where the perceived distances between the frequencies are plotted more or less equidistantly.

Because we're working on the frequency space, and in most "real-life" examples, frequencies don't immediately jump around (unlike the values of raw sound), we don't need as much resolution in the temporal axis. This reduction of data makes it easier for an algorithm to learn longer-term dependencies, and a lighter, faster model can be used.

## Auto-encoder

Autoencoders are simple, they are made of an encoder and a decoder.

The encoder compresses the data and the decoder decompresses the data. Because there's a bottleneck, this is generally a lossy process.

There are multiple uses for an autoencoder: removing noise, compressing data, and even creating deepfakes.

Applying an encoder can be similar to applying [PCA](https://en.wikipedia.org/wiki/Principal_component_analysis), however, most times, because we won't use linear activation functions, the encoder is capable of capturing non-linear relationships.

We train this by minimizing the reconstruction error of the system. So we feed it an image of a dog, let's say, and through the compression and decompression processes that happen within the autoencoder, we should get the same image at the end. For some use cases that would be the perfect autoencoder, for others not so much (it would be unable to remove the noise of an image for example).

So we compress the data into a _latent space_, this latent space keeps the most important attributes of the input data. If the encoder is good, everything else will not be represented by this space.

