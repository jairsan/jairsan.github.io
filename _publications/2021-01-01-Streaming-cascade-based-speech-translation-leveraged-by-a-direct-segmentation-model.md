---
title: "Streaming cascade-based speech translation leveraged by a direct segmentation model"
collection: publications
permalink: /publication/2021-01-01-Streaming-cascade-based-speech-translation-leveraged-by-a-direct-segmentation-model
date: 2021-01-01
venue: 'Neural Networks'
---
This paper extends the previous one (EMNLP2020) with additional experiments and by moving from a simulated Streaming
scenario, which used an offline MT system, to a real streaming scenario with a simultaneous MT system.

[Access paper here](https://doi.org/10.1016/j.neunet.2021.05.013){:target="_blank"}

This is how I described this publication in my thesis:

<blockquote>
This paper extends the previous one by moving from a simulated streaming
scenario into a real one. Previously, we worked on a simulated
scenario which used the fixed transcriptions of an ASR system and an offline 
MT system to asses the feasibility of the proposed DS system. This 
work uses streaming ASR and MT systems whose hyperparameters
are jointly optimized with the DS segmenter in order to maximize the
latency-quality trade-off of the streaming process, and the streaming
scenario is tested by using as input the raw, unsegmented interventions
of the Europarl-ST corpus.  
</blockquote>

<blockquote>
The experiments are carried out with a Spanish ASR system, and 
Spanish-English and Spanish-French MT systems, which highlights how Europarl-ST
enables non-English centric ST. Two online MT approaches are tested, MMAH and
wait-k translation, and our experiments show how, for these settings, wait-k
is the preferred approach in both quality and latency.
</blockquote>