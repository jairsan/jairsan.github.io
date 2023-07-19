---
title: 'Lessons learned from developing research software: Updating the Streaming Segmenter'
date: 2023-07-19
permalink: /posts/2023/07/updated_segmenter/
layout: splash
tags:
  - speech translation
  - software
---
  
# Introduction
I have recently released an updated version of my [Streaming Text Segmenter software](https://github.com/jairsan/Speech_Translation_Segmenter),
which I published at the start of my PhD. This was my first "serious" software project, the results were quite
nice, and it has played quite an important role for my research and technology transfer activities over these
past 3 years. I have grown a lot during this time, so I want to summarize the main changes that I have applied over
the years, and what important lessons I learned along the way.

## The Streaming Text Segmenter
To give some background, I'm going to briefly describe what the Streaming Text Segmenter, but the lessons contained here
can be applied to any Machine Learning (ML) system. 

In a cascaded Speech Translation (ST) scenario, two systems
are used: an Automatic Speech Recognition (ASR) system which transcribes the audio,
and a Machine Translation (MT) system that translates the transcriptions into the target language. A true streaming ASR
system takes a continuous audio stream as input, and produces a continuous transcription stream in a real-time manner. We
thus need some sort of component that takes this unpunctuated transcription stream and chunks it into (hopefully) sentence-like
units that make semantic sense. This is quite an important task because it significantly conditions the quality of the downstream
translation.

My proposal was to use a task-specific segmenter model, which is a classifier that given a sequence of text tokens (and optionally some audio features)
decides whether or not this is a complete segment.

# Lessons learned
Keep in mind that most of these ideas are shaped by the fact that my PhD was quite applied (my research group has many
technology transfer contracts) and that I'm also working on an applied research position in industry. If your sole focus
is trying new ideas and you don't care to whether these are actually used later, some of these points might not be so relevant.
Of course, they might also be wrong and you are quite free to disagree with them.

I'm a strong believer in open and collaborative science, and to me, a successful research idea (more about this on a future blog post)
is not one that is merely
published, but rather an idea that has a long lasting impact and that is adopted by the community. These lessons are
not only focused on the part about getting your paper accepted, but rather on the whole lifecycle of the idea, which ideally
includes some future refinements.

I have collected my ideas into a list of Do's and Don'ts. I will give some general overview for each one, and then I will
try to illustrate it with some example.

## Do: Follow good engineering practices
Unfortunately, software quality seems to be one of the first things that is sacrificed in the pursuit of new publications.
In fact, if one wishes to maximize the number of publications, it might be an OK strategy to implement something very
quickly to try to get some publishable results, but this tends to lead to problems later down the road. There is this
tendency to think that research code is a disposable tissue that can be discarded after you get a publication, but if
your idea is promising, it probably means that there are many ways that it can be expanded.

If you spend a little bit more time in writing quality code and documentation, the task of following up on this will be
significantly easier for you or other researchers that want to build on top of it. Even if you never plan on releasing the
code, your future self will thank you. Due to the nature of the publication process, as well as the fact that experiments
take a long time to run, is not unusual to be involved in multiple projects at the same time, and come back to each one
every couple of days/weeks/months. I don't know about you, but unless I document everything, I forget most of the
details of my codebase in very little time. This is one of the main reasons why I think that it is not just a matter
of learning proper development practices, but that there is a very real return in productivity gained by developing
quality software.

## Do: Keep it (conceptually) simple
In the current SOTA-chasing climate, it is quite difficult to avoid the temptation of trying dozens of
different tweaks just to shave a couple decimal points from the leaderboard, but this might not be the best use of your
time.

It is important to not lose track of the actual research questions (RQ)  you are trying to answer in your work. Before
implementing something, ask yourself, is this feature related to the main claims of the paper,
or is it just another "trick" to try to surpass the competition? Keep it mind that, for a fair comparison,
any trick you come up with should also be applied to your selected baseline.

Of course, this doesn't mean that you should completely ignore system performance. I'm a strong believer that any
research idea should be tested with a somewhat realistic setup, otherwise your findings might not translate to the
real world. Focusing too much on performance rather than your main RQs makes it difficult to know where are improvements
actually coming from, but can add a significant amount of complexity to the codebase.

I actually have an endless amount of examples about this. When I first started my PhD, I was very much into my "brainstorm and
try everything phase", so I wasted a lot of time implementing "tricks" into the software. For example, I tried 
randomly masking out words in some of the training samples of the segmenter to see if this would
slightly improve performance. I do not actually recall if this had any
effect, but I ended up removing this feature when I refactored the software (many other dubious features
also suffered the same fate for the v1.0 release, as I tried to only keep the important parts). The thing is, this was not really related
to the goal of the original publication, so it was a detour to no-where.

## Do: Refactor soon, refactor often 
This is somewhat related to the first point about quality standards. At some point you are going to reach a situation
where your original idea needs to be significantly altered. Try to avoid as much as possible applying "ugly" fixes like
creating "_V2" versions of things. This might be OK for a one-shot experiment, but if you are not careful, you can quickly
end up with a codebase full of permanent "temporary" fixes and you end up losing track of everything. This can also be a
major source of bugs.

If you invest some time into refactoring your code, you might be surprised how often you realize that things are now
much easier to implement after a refactor, which can further speed-up your experimentation.

Some time after the original publication, we decided that it might be interesting to try to jointly segment and recover
punctuation marks, so the problem changed from 2-class classification (continue/end-of-chunk) no n-class classification
(continue/end-of-chunk+comma/end-of-chunk+period/...). The code was not originally prepared for this, and a refactor
would have been the appropriate choice. Instead, I created a "_multiclass" version of many of the classes. Then,
some more development happened on top of this, and suddenly the original code was not up to date because it
did not include some features that I developed for the "_multiclass" versions. As you can imagine, this was a huge mess 
that could have been easily avoided by devoting just a little time to do a proper refactor.

## Don't: Implement everything from scratch
Some students seem to have the idea that their research project is somehow less worthy if they build on top of 
existing implementations instead of doing everything from scratch. Your time is best spent focusing
on what actually matters, your RQs, rather than boilerplate code.

Of course, there are times where an existing solution doesn't work, or it would take too much effort
to adapt to your use case, and in that case it is better to start from scratch. But in general, there are many tools
to make your life easier, either at the framework level, or some existing implementation for a similar
research problem.

In my case, I remember I spent a lot of time trying to come up with solutions to fit huge datasets on
the memory-limited machines of my university. For the v1.0 release, I just used the HuggingFace Datasets library,
and it worked flawlessly.

## Don't: Overlook performance
Most ML practitioners face a daily battle that consists in choosing whether to allocate more time
to running and monitoring experiments or to further code development. Unless you are on a very well
funded lab with plenty compute power, many times you will be limited by your access to computational
resources rather than your developer time. Thus, once your proof-of-concept works, it generally makes
sense to devote some time to improve the efficiency of your solution before you launch a barrage of
experiments to populate the tables of your paper. This doesn't need to be anything fancy. The use of
some well tested techniques such as FP16 training, as well as taking care of removing data loading
bottlenecks and adjusting the batch size to saturate the GPU can significantly speed-up the computations.
There is going to be a point of diminishing returns where it doesn't make any sense to invest hours just
to reduce runtime by a couple percentage points, but there are many easy to implement techniques that you
should apply before
you get to that point.

Specifically, FP16 training is a clear winner in this category in my opinion. It almost doubles your
training speed, and it has now been integrated across many frameworks, so it's easier to use than ever.
This was specially helpful when doing experiments with pre-trained models. 

# Conclusions
I have mainly talked about the most relevant problems I found during my PhD journey, but this is
of course not an exhaustive list. The main takeaway is to avoid thinking of our research software
as a disposable commodity, and start thinking about the greater picture and how the code could
be used in the future. This leads to greater focus on good engineering practices.

I could have never imagined that I was going to use this software for so many research and technology
transfer projects, and I had to learn a lot of hard lessons along the way, but overall I am quite happy
about how it turned out. Hopefully this post has given you some inspiration that will help you
build your next revolutionary research idea!