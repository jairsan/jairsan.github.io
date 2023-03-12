---
title: "From Simultaneous to Streaming Machine Translation by Leveraging Streaming History"
collection: publications
permalink: /publication/2022-01-01-From-Simultaneous-to-Streaming-Machine-Translation-by-Leveraging-Streaming-History
date: 2022-01-01
venue: 'ACL 2022'
---
The Streaming ST scenario presents many challenges, but there are also opportunities that can be used to improve
translation quality. This work introduces the concept of Streaming history, which holds the information
of the previously translated segments. The proposed MT system is able to leverage this contextual information
in order to improve translation quality. 

[Access paper here](https://doi.org/10.18653/v1/2022.acl-long.480){:target="_blank"}

This is how I described this publication in my thesis:

<blockquote>
This paper presents a general methodology for building context-aware
state-of-the-art streaming MT systems, by incorporating the previously
developed DS streaming segmenter and using our proposed streaming
metrics for evaluation. This publication takes advantage of the
insights developed in the previous publications in order to
build a strong streaming baseline MT system, and improves it
with a novel context-aware training methodology which obtains
significant improvements. Further improvements are also obtained
with a proposed Partial Bidirectional Encoder (PBE) that has access
to a larger portion of the input prefix.
</blockquote>

<blockquote>
Our approach is similar to the concatenative approach used
in context-aware MT, and uses a sliding window which contains
the previous streaming history that has been produced during
the translation process. History-augmented training samples are
constructed from document-level corpora, and at inference time,
the real streaming history is used. Extensive experiments
are carried on IWSLT English-German data in order to 
study the behaviour of the model and optimize
the latency-quality trade-off. Using our proposed 
streaming latency metrics, our system is compared with the 
ACT streaming approach (Schneider and Alexander Waibel 2020) and the submissions
to the IWSLT 2020 simultaneous track (Ansari et al. 2020), achieving a similar level
of quality for a fraction of the latency.
</blockquote>
