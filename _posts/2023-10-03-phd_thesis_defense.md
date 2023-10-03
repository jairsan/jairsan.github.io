---
title: "My PhD Thesis Defense"
date: 2023-10-03
permalink: /posts/2023/10/thesis_defense/
layout: splash
tags:
  - phd
  - academia
  - life
---
  
# PhD Defense
I successfully defended my PhD on the 29th of September. At the time I was feeling a whirlwind of emotions that were
quite difficult to articulate. Now I feel like I am ready to share some more details about that day.

## My thesis
The topic of my thesis is 'Streaming Neural Speech Translation'. The last part is easy to understand: the thesis is about
Speech Translation (translation of the contents of an audio signal into text in a different language) using
Neural Networks. But what does it mean for it to be 'Streaming'?

We define an ST system as Streaming if it:
- Generates the translation out of a partial input prefix (This is sometimes known as simultaneous or online capability), and
- Can handle the translation of a (possibly) unbounded input stream.

It is not enough to translate a short audio utterance. Our system must be able to,
for example, translate (under real-time constraints) a long university lecture which is being
delivered (live) by a distinguished visiting scholar. This is a much more exciting (and complicated!) problem
that the standard simultaneous ST setup.

As part of my thesis I have published 4 major contributions (you can click on each one to go to a little page with more information):
- [A multilingual dataset for ST: Europarl-ST](/publication/2020-01-01-Europarl-ST-A-Multilingual-Corpus-for-Speech-Translation-of-Parliamentary-Debates)
- [A segmenter component that splits the transcription into sentence-like units to be translated by the MT system](/publication/2020-01-01-Direct-Segmentation-Models-for-Streaming-Speech-Translation)
- [A method for computing the latency of an ST system for the Streaming scenario](/publication/2021-01-01-Stream-level-Latency-Evaluation-for-Simultaneous-Machine-Translation)
- [A method for using contextual information from the translation stream in order to improve translation quality](/publication/2022-01-01-From-Simultaneous-to-Streaming-Machine-Translation-by-Leveraging-Streaming-History)

It's hard to judge the quality of your own work, specially with so many personal feelings attached to it, but I really feel like I 
made a significant contribution to the field. The Europarl-ST corpus has been, without a doubt, the major star of my thesis,
and I'm amazed at how fast it was adopted by the community as a standard benchmark for ST. Nowadays there are multiple
ST corpus that have more data, but at the time it was something unique because high-quality ST data simply did not exist
for many language pairs. I particularly liked working on the the third and fourth contributions, as they were two issues
that I felt were being overlooked by the community, and it felt great to be able to make a proposal in how to address them.
The response that I have received from the research community has exceeded my wildest expectations, and I will fondly
remember many of the discussions that I had at the conferences where I presented this work. I can only hope that I continue
to produce research such as this in the future.

## Some background 
For those of you that don't know, in Spain (and in most academic systems as far as I know)
a PhD defense consists in a presentation of your thesis to a defense committee, followed by a 
long session of questions. These questions are typically related to some specific part of your work,
but they might also be more open ended questions about how your research contributes to move the field forward, or
how your work could be expanded. 

In order to avoid any misunderstanding, it's important to highlight the fact that the PhD Defense is more a rite of
passage rather than a pass/fail exam. In fact, I do not know of a single person that has failed at the defense stage. The real obstacles for getting a PhD is fulfilling the requirements
set by the university for your PhD program (for example, they might ask for a given number of scientific publications before
you can graduate) as well as convincing your advisor that you have made a significant contribution. 

Most of the pressure 
is therefore self-inflicted, as the defense session is the result of many years of hard work, and you would
like this hard work to be recognized by the attendees. I'm not trying to say that the actual defense is not important,
in fact I spent close to a month preparing everything, but its importance needs to be contextualized. I have always
tought that a PhD is achieved as a result of many small steps, rather than a single defining moment, and this is more of the
same. You will have already earned a PhD by the point you get to your defense, rather than the other way around.

## The actual defense
I'm very happy with how everything turned out during the defense. In fact, it would be hard to try to come
up with any substantial improvement apart from some very minor details, such as some body language things that could have been improved
at times. But taking into account that I was quite nervous, specially during the first 10 to 15 minutes, I really can't
complain about the results. 

The defense committee was very positive about my contributions, and they highlighted the fact
that my research has been peer-reviewed and published at some of the top venues for NLP and Speech. The actual defense
was not recorded, but I have prepared a video where I go over the same content, so that you might learn more about my
research, as well as seeing what the first stage of a PhD defense looks like: [https://youtu.be/aqzrFUnCy1I](https://youtu.be/aqzrFUnCy1I)

## Next steps
I have left academia and I'm now working full-time as an Applied Scientist at AppTek (previously I worked there part-time during the
end stages of my PhD). I think this mix of research and product development suits me quite well, as I have always been interested in applied research and turning this into an actual product. I plan to go more into
detail about this decision on a future post, but for now know that even if it means stepping out of my comfort zone, I am sure
that it is the right decision.

My work at AppTek is closely related to my PhD (I'm part of the AppTek Speech Translation team), so you can expect 
additional contributions on this area. There is also some additional work that I carried out after finishing my PhD document
but before my defense,
and this work is yet to be published, so that is something that you can expect to see at some near future.

