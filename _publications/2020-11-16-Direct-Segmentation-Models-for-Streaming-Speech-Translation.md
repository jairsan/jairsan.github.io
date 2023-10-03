---
title: "Direct Segmentation Models for Streaming Speech Translation"
collection: publications
permalink: /publication/2020-01-01-Direct-Segmentation-Models-for-Streaming-Speech-Translation
date: 2020-11-16
venue: 'EMNLP'
---
Machine Translation systems are trained with full sentences, but in the Cascaded Speech Translation scenario, the 
output of the ASR system does not necessarily form sentences, which hampers performance. This publication introduces
a streaming-ready segmenter applied to the output of the ASR system, in order to maximize downstream translation quality.

[Access paper here](https://doi.org/10.18653/v1/2020.emnlp-main.206){:target="_blank"}

[Access the code here](https://github.com/jairsan/Speech_Translation_Segmenter){:target="_blank"}

This is how I described this publication in my thesis:
<blockquote>
This paper studies how to optimize the processing and segmentation of the ASR system output so that
the downstream MT performance is maximized. Specifically, this publication is focused on studying
the segmentation problem for the streaming scenario. We introduce a novel neural segmenter
architecture, Direct Segmentation (DS), which considers the segmentation process as a classification problem. Using a sliding
window approach, for every position of the ASR stream, the segmenter decides whether or not
to produce a chunk by using a fixed local history and a small look-ahead window. 
The performance of this approach is evaluated on the previously introduced Europarl-ST corpus,
by training an offline MT system and testing its performance when combined with different
segmenters, for the English $\leftrightarrow$ \{German, French, Spanish\} directions. Experiments
are also performed showing that adding audio features to the segmenter improves performance.
</blockquote>

<blockquote>
The proposed architecture is computationally efficient while outperforming other segmentation
approaches, and is able to work straight-out-of-the box in the streaming scenario. Additionally,
the work studies how the MT training data
should be processed so that it better matches the ASR transcriptions,
avoiding the need for an intermediate inverse text normalization step.
</blockquote>
