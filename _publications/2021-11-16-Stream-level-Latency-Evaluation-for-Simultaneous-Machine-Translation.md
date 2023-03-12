---
title: "Stream-level Latency Evaluation for Simultaneous Machine Translation"
collection: publications
permalink: /publication/2021-01-01-Stream-level-Latency-Evaluation-for-Simultaneous-Machine-Translation
date: 2021-11-16
venue: 'Findings of EMNLP 2021'
---
A reliable evaluation metric is critical for any technical and scientific task. However, the standard simultaneous
MT latency metrics (AP, AL and DAL) are not robust when applied to the Streaming scenario. This paper studies this phenomenon
and proposes a re-segmentation solution that provides reliable and interpretable results for the Streaming scenario.

[Access paper here](https://doi.org/10.18653/v1/2021.findings-emnlp.58){:target="_blank"}

[Access the code here](https://github.com/jairsan/Stream-level_Latency_Evaluation_for_Simultaneous_Machine_Translation){:target="_blank"}

This is how I described this publication in my thesis:

<blockquote>
This paper introduces a novel evaluation procedure for streaming MT. Standard
online MT metrics only work with short audio segments, evaluated in isolation,
and do not take into account the sequential nature of the streaming scenario.
Our proposed streaming evaluation method fixes these issues, and as a bonus,
it can be applied to the standard metrics used for online MT with a small
modification. Our proposal keeps track of a global latency score
across the entire translation process, and uses a re-alignment step that matches
translated words with the correct reference segment.
</blockquote>

<blockquote>
A significant advantage of our proposal is that the evaluation procedure
is not system/segmentation dependent and can be used
to compare different systems, as well as maintaining
the original interpretability of the metrics. Comparative experiments
show that, unlike competing approaches, our proposal correctly ranks
systems based on their latency, as well as keeping the previously
mentioned properties.
</blockquote>

