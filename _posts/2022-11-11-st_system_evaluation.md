---
title: 'An overview of Speech Translation Evaluation'
date: 2022-11-11
permalink: /posts/2022/11/st_system_evaluation/
layout: splash
tags:
  - speech translation
  - evaluation
---
  
# Introduction
Welcome to the first technical blog post of this page. The goal of this post is to provide a general overview of the evaluation process
for a standard Speech Translation (ST) setup. The focus will be to highlight the differences of the ST
setup with the standard Machine Translation (MT) scenario. Thus, the reader should have some basic MT knowledge in order to
follow along.

This paper is organized as follows. [Section 1](#part1) presents a general overview of ST evaluation and
the challenges
of how to conduct it. [Section 2](#part2) presents a practical example of ST evaluation.
 For this, we will evaluate English to Spanish translation, using the Europarl-ST test set.
For simplicity, we are going to use pre-trained Automatic Speech Recognition (ASR) and MT models instead
of training our own.

There are many improvements that could be applied to this setup, both in terms of quality and efficiency, but this
is not today's focus. Please refer to [Appendix A](#ap1) for a more in-depth discussion about model choice and general improvements.

All the files used for this blog post are available at the [jairsan_web_experiments repo](https://github.com/jairsan/jairsan_web_experiments/tree/main/2022-11-11_st_system_evaluation) 

# Speech Translation Evaluation {#part1}
When working with standard academic datasets (MuST-C and Europarl-ST are the most popular choices), ST can be approached as a standard MT task that has pre-computed source audio segments instead
of source sentences. You can then run ASR inference for each segment, and feed the transcription to the MT system. Because
each audio sample is paired with a reference translation, sacrebleu can be directly run with the system hypothesis and the only
difference with the standard MT case would be the addition of the ASR system. 

However, in the real world, we do not have the luxury of pre-computed, gold-standard segmentation, and instead our system
receives as input the whole unsegmented audio signal. Standard ASR and MT systems are trained to perform inference over short audio segments
or sentences instead of unbounded streams. Thus, when running realistic experiments, we have to incorporate
a segmentation step into the pipeline, which must produce segments that are similar to those used during system training.
Research has shown that this is a critical step that has significant impacts on translation quality. 

Furthermore, the segmentation step will almost surely produce a different number of segments than reference sentences,
so we cannot directly evaluate our hypothesis. Even if by some freak coincidence the same number of segments were produced,
the contents of the *n*-th hypothesis segment and the *n*-th reference segment will surely be different.
The following image, adapted from the [EACL 2021 tutorial on Speech Translation](https://aclanthology.org/2021.eacl-tutorials.3/), illustrates this fact. 

![Speech Translation Evaluation, adapted from Niehues 2021](/images/my_images/st_evaluation.png)

You can see how our ST system produced 5 segments/sentences, but the reference contains 3 sentences. On this example,
the ST system produced a perfect translation, but in the real world the ST system would
have produced an imperfect translation with no clear alignment between hypothesis and reference. Thus, we are faced
with the fact that we have: 
- a hypothesis consisting of *n* segments/sentences 
- a reference translation consisting of *m* segments/sentences, with *n* != *m*
- no clear mapping between hypothesis and reference

A clever way to fix this problem is to carry out re-segmentation of the system hypothesis, that is, generating a new
set of *m* segment boundaries for the hypothesis, so that it is split into the same number of segments as the reference.
This is done by computing a word alignment between hypothesis and
reference that minimizes the Levenshtein edit distance at the word level. This produces optimal segment boundaries so
that the content of the new segments better matches those of the reference.

Applying this to the previous example would produce the following 3 segments:
- This is Signal.
- In the training data it was split using strong punctuation.
- Three sentences in total!

This evaluation strategy was proposed at i6 in a [2005 IWSLT paper](https://aclanthology.org/2005.iwslt-1.19/), and it has become
the standard evaluation setup used in Speech Translation, including the IWSLT yearly competition. 

# Practical example {#part2}
## Setup
I created a conda environment using this [environment.yml](https://github.com/jairsan/jairsan_web_experiments/blob/main/2022-11-11_st_system_evaluation/environment.yml),
which is the one included with SHAS, but with CUDA 10 replaced by CUDA 11. Then I installed the other software dependencies:
```shell 
conda env create -f environment.yml
conda activate shas
pip install librosa
git clone https://github.com/mt-upc/SHAS.git
git clone https://github.com/jairsan/Stream-level_Latency_Evaluation_for_Simultaneous_Machine_Translation.git
cd Stream-level_Latency_Evaluation_for_Simultaneous_Machine_Translation/
pip install .
```

You will also need to download the pre-trained English SHAS model, and the Europarl-ST data.

```shell 
#Manually download SHAS English model: https://drive.google.com/u/0/uc?export=download&confirm=DOjP&id=1Y7frjVkB_85snZYHTn0PQQG_kC5afoYN
wget https://mllp.upv.es/europarl-st/v1.1.tar.gz
tar xvzf v1.1.tar.gz
```

We are then going to downsample the audio to 16KHz, which is a requirement of the segmentation model.

```shell
BASE_DIR=$PWD/v1.1/
src=en
tgt=es
set=test

mkdir -p data_"$set"_raw

cat $BASE_DIR/$src/$tgt/$set/speeches.lst | while read speech;
do
    ffmpeg -i $BASE_DIR/$src/audios/$speech.m4a -nostdin -ac 1 -ar 16000 -hide_banner -loglevel error data_"$set"_raw/$speech.wav
done
```

## Segmentation
We are going to use [SHAS](https://github.com/mt-upc/SHAS) in order to segment the raw audio into chunks/segments. Each
segment will then be transcribed and translated independently of the others.

```shell
AUDIO_LOC=$PWD/data_test_raw
SHAS_ROOT=$PWD/SHAS
out_folder=$PWD/data_test_segmented
path_to_wavs=$AUDIO_LOC/

set=test
maxlen=10;
path_to_custom_segmentation_yaml=$out_folder/maxlen"$maxlen"_segmentation.yaml

thres=0.3

python3 ${SHAS_ROOT}/src/supervised_hybrid/segment.py \
  -wavs $path_to_wavs \
  -yaml $path_to_custom_segmentation_yaml \
  --dac_threshol $thres \
  -ckpt en_sfc_model_epoch-6.pt \
  -max $maxlen
```

This will produce a .yaml file containing the information of the segments detected by SHAS.
```shell
$ head -n 5 data_test_segmented/maxlen10_segmentation.yaml
[{duration: 3.9439, offset: 0.2402, rW: 0, speaker_id: NA, uW: 0, wav: en.20080924.31.3-249.wav},
  {duration: 9.3493, offset: 4.6446, rW: 0, speaker_id: NA, uW: 0, wav: en.20080924.31.3-249.wav},
  {duration: 2.4224, offset: 14.4545, rW: 0, speaker_id: NA, uW: 0, wav: en.20080924.31.3-249.wav},
  {duration: 8.0881, offset: 17.2573, rW: 0, speaker_id: NA, uW: 0, wav: en.20080924.31.3-249.wav},
  {duration: 9.9499, offset: 25.9459, rW: 0, speaker_id: NA, uW: 0, wav: en.20080924.31.3-249.wav},
```

## Transcription
We will now transcribe the segments using a Wav2Vec2 model finetuned on LibriSpeech, which is a standard
benchmark for ASR systems. We are going to prepare a transcribe.py with the following content:
```python
import sys
import yaml
import torch
from transformers import Wav2Vec2Processor, Wav2Vec2ForCTC
import librosa


def transcribe(segmentation_yaml:str, audio_folder:str):

    model = Wav2Vec2ForCTC.from_pretrained("facebook/wav2vec2-large-960h-lv60").to("cuda")
    processor = Wav2Vec2Processor.from_pretrained("facebook/wav2vec2-large-960h-lv60", do_upper_case=False)

    with open(segmentation_yaml) as segmentation_file:
        items = yaml.safe_load(segmentation_file)
        for segment in items:
            audio_file = audio_folder + "/" + segment["wav"]
            waveform, _ = librosa.load(audio_file, sr=16000, offset=segment["offset"], duration=segment["duration"])
            features = processor(waveform, sampling_rate=16000, padding=True, return_tensors="pt")
            features = {key: value.to("cuda") for key, value in features.items()}

            logits = model(**features).logits

            predicted_ids = torch.argmax(logits, dim=-1)
            transcription = processor.batch_decode(predicted_ids)
            # LS transcriptions are uppercase, so we lowercase
            print(transcription[0].lower())


if __name__ == "__main__":
    transcribe(sys.argv[1], sys.argv[2])    
```

```shell
$ python3 transcribe.py data_test_segmented/maxlen10_segmentation.yaml data_test_raw > data_test_transcribed
$ head -n 5 data_test_transcribed 
is only greed euphoria and cheap money to be blamed for the whole mess
what about the flaws of the region at an distribumata which has inhansed systemac risk what about scewed paste cims with the lack of affects which have stimulated reckless ristaking
what about investment great values assigned to trash
at about comflicts of interest what aot banks engaging in casino type transactions what about the shadow banking sector withis extreme laveraging and speculations
why haven't policy make it learn from previous crises those stern warnings were sent just t remember what lumfa lucy gramlick folker buff had said years ago
```

The transcriptions are not particularly great, but we now have obtained sentences that can be translated by the MT model.
##  Translation
This step is the same as in the standard MT case. We take the transcriptions as input sentences and 
generate the translations, using the M2M100 model and the following translate.py file:
```python
import sys
from transformers import M2M100ForConditionalGeneration, M2M100Tokenizer


def translate():
    model = M2M100ForConditionalGeneration.from_pretrained("facebook/m2m100_1.2B").to("cuda")
    tokenizer = M2M100Tokenizer.from_pretrained("facebook/m2m100_1.2B", src_lang="en")
    for line in sys.stdin:
        input_words = line.strip().split()
        if len(input_words) == 0:
            print("")
        else:
            inputs = tokenizer(line, return_tensors="pt")
            translated_tokens = model.generate(input_ids=inputs["input_ids"].to("cuda"),
                                               attention_mask=inputs["attention_mask"].to("cuda"),
                                               forced_bos_token_id=tokenizer.lang_code_to_id["es"])
            print(tokenizer.batch_decode(translated_tokens, skip_special_tokens=True)[0])


if __name__ == "__main__":
    translate()
```

```shell
$ cat data_test_transcribed | python3 translate.py > data_test_translated
$ head -n 5  data_test_translated
Sólo la avaricia eufórica y el dinero barato son culpables de toda la confusión.
¿Qué hay de los defectos de la región en un distribuumata que ha inhansed sistemaac riesgo ¿qué hay de las cimas de pasta esguiladas con la falta de efectos que han estimulado ristaking imprudente
¿Qué pasa con los grandes valores de inversión asignados a la basura?
En cuanto a los conflictos de intereses ¿Qué son los bancos que se involucran en las transacciones de tipo casino ¿Qué es el sector bancario de sombra? ¿Qué es la especulación y la especulación extrema?
¿Por qué la política no ha hecho que aprenda de las crisis anteriores que las advertencias severas fueron enviadas sólo no recuerda lo que Lumfa lucy gramlick folker buff había dicho hace años
```

##  Re-segmentation and evaluation
Traditionally, the resegmentation step
was done using the [mwerSegmenter binary](https://www-i6.informatik.rwth-aachen.de/web/Software/mwerSegmenter.tar.gz).
Earlier this year, researchers at Apptek released a Python implementation of minimum-edit-distance re-alignment as part of
the [SubER software](https://github.com/apptek/SubER), which I find to be more practical and robust than the MWER binary.
I have integrated this implementation into the software of my [EMNLP 2021 Findings paper](https://github.com/jairsan/Stream-level_Latency_Evaluation_for_Simultaneous_Machine_Translation),
which is what we will be using today. The resegmentation procedure is implemented with the ```stream_resegment``` command.
```shell
stream_resegment [-h] --hypo_file HYPO_FILE --reference_file REFERENCE_FILE [--output_file OUTPUT_FILE]

optional arguments:
  -h, --help            show this help message and exit
  --hypo_file HYPO_FILE
                        File to be resegmented, contains the system hypothesis.
  --reference_file REFERENCE_FILE
                        Reference file to be used for resegmentation. The output will be segmented into the same number of lines as this file.
  --output_file OUTPUT_FILE
                        If set, the resegmented output is stored on this file instead of stdout.
```

We can apply this to the hypothesis in order to obtain a resegmented hypothesis.

```shell
$ stream_resegment --hypo_file data_test_translated --reference_file v1.1/en/es/test/segments.es > data_test_translated.resegmented
$ wc -l data_test_translated data_test_translated.resegmented v1.1/en/es/test/segments.es
  1603 data_test_translated
  1267 data_test_translated.resegmented
  1267 v1.1/en/es/test/segments.es
  4137 total
```
Notice how the hypothesis has been resegmented in order to match the reference. If you devote some time to examine the
resegmented file you will see how the segment boundaries better match those of the reference.

`stream_resegment` can be combined with `sacrebleu` in order to painlessly evaluate our hypothesis regardless of the
underlying segmentation used by our model.

```shell
$ stream_resegment --hypo_file data_test_translated --reference_file v1.1/en/es/test/segments.es | sacrebleu --language-pair en-es v1.1/en/es/test/segments.es
BLEU+case.mixed+lang.en-es+numrefs.1+smooth.exp+tok.13a+version.1.5.0 = 29.0 61.1/37.8/25.3/17.5 (BP = 0.911 ratio = 0.915 hyp_len = 30596 ref_len = 33441)
```

# Conclusions
ST presents a particular set of challenges that must be addressed. Unless one wants to work in an artificial academic
setting, one should take into account audio segmentation, which is not a trivial matter. Using the right tools
can address these issues and make your life easier, while at the same time ensuring that you are running a
realistic evaluation. High quality segmentation can be obtained with SHAS,
and hypothesis re-segmentation is easy to apply with `stream_resegment`.

## Appendix A: System choice and further improvements {#ap1}
Note that there are also End-to-End ST models
that skip the ASR step and produce the translation from the original audio. At the time of writing this post,
their quality performance is
on-par with the cascaded ASR+MT approach, so that is why I elected to use the classical approach.

The LS ASR system used in this post is not the best when it comes to transcribing parliamentary speeches, but I selected
it because there is no overlap between the training data and the Europarl-ST test data.
Additional fine-tuning with in-domain data (Europarl-ST train set), or adding an in-domain Language Model would 
improve transcription quality substantially.

Unfortunately, the M2M model has been trained 
using Common Crawl data, which probably includes the European Parliament website from which Europarl-ST was constructed.
I was not able to find any other publicly available model for which I know with 100% certainty that it does not 
include Europarl-ST, so I begrudgingly ended up using M2M. I might look further into this issue in the future, but for
now it falls outside the scope of this blog post.

The transcription/translation is not using batched inference, which would significantly decrease inference time.