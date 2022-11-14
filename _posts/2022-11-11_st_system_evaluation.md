---
title: 'Introduction'
date: 2022-11-11
permalink: /posts/2012/08/blog-post-1/
tags:
  - speech translation
  - evaluation
---
  
# Introduction
TODO change asr model to https://huggingface.co/facebook/wav2vec2-large-960h-lv60 and rerun

Welcome to the first technical blog post of this page. The goal of this post is to provide a general overview of the evaluation process
for a standard Speech Translation (ST) setup. We will take as a starting point an already trained ASR, as well as an MT one,
and we will run both inference and evaluation with them. The goal will be to highlight the differences of the ST
setup with the standard MT scenario. For this, we will focus on English to Spanish translation, using the test set
of the Europarl-ST corpus.

The actual models used on this post are simple pre-trained models, and there are many ways the results could be
improved, both in terms of quality and efficiency. In fact, the actual quality results are quite mediocre, but this System performance is not the topic of this blog post, please refer to Appendix A for more information on this.

All the files used for this blog post are available at: https://github.com/jairsan/jairsan_web_experiments/tree/main/2022-11-11_st_system_evaluation

# Segmentation

## Setup

I created a conda environment using this https://github.com/jairsan/jairsan_web_experiments/blob/main/2022-11-11_st_system_evaluation/environment.yml,
which is the one reccomended by SHAS but using CUDA 11 instead of CUDA 10. Then I installed the other software dependencies.
```shell 
conda env create -f environment.yml
conda activate shas
pip install librosa
git clone https://github.com/mt-upc/SHAS.git
git clone https://github.com/jairsan/Stream-level_Latency_Evaluation_for_Simultaneous_Machine_Translation.git
cd Stream-level_Latency_Evaluation_for_Simultaneous_Machine_Translation/
pip install .
```

You also need to download the pretrained english SHAS model, and the Europarl-ST data.

```shell 
#Manually download https://drive.google.com/u/0/uc?export=download&confirm=DOjP&id=1Y7frjVkB_85snZYHTn0PQQG_kC5afoYN
wget https://mllp.upv.es/europarl-st/v1.1.tar.gz
tar xvzf v1.1.tar.gz
```

# Transcription
```python
import sys
import yaml
from transformers import Speech2TextForConditionalGeneration, Speech2TextProcessor
import librosa

def transcribe(segmentation_yaml:str, model_name:str, audio_folder:str):

    model = Speech2TextForConditionalGeneration.from_pretrained("facebook/" + model_name).to("cuda")
    processor = Speech2TextProcessor.from_pretrained("facebook/" + model_name, do_upper_case=False)

    with open(segmentation_yaml) as segmentation_file:
        items = yaml.safe_load(segmentation_file)
        for segment in items:
            audio_file = audio_folder + "/" + segment["wav"]
            waveform, _ = librosa.load(audio_file, sr=16000, offset=segment["offset"], duration=segment["duration"])
            features = processor(waveform, sampling_rate=16000, padding=True, return_tensors="pt")
            input_features = features.input_features.to("cuda")
            attention_mask = features.attention_mask.to("cuda")

            gen_tokens = model.generate(input_ids=input_features, attention_mask=attention_mask)
            transcription = processor.batch_decode(gen_tokens, skip_special_tokens=True)

            print(transcription[0])

if __name__ == "__main__":
    transcribe(sys.argv[1], sys.argv[2], sys.argv[3])    
```
# Translation
```python
import sys
import yaml
from transformers import Speech2TextForConditionalGeneration, Speech2TextProcessor
import librosa

def transcribe(segmentation_yaml:str, model_name:str, audio_folder:str):

    model = Speech2TextForConditionalGeneration.from_pretrained("facebook/" + model_name).to("cuda")
    processor = Speech2TextProcessor.from_pretrained("facebook/" + model_name, do_upper_case=False)

    with open(segmentation_yaml) as segmentation_file:
        items = yaml.safe_load(segmentation_file)
        for segment in items:
            audio_file = audio_folder + "/" + segment["wav"]
            waveform, _ = librosa.load(audio_file, sr=16000, offset=segment["offset"], duration=segment["duration"])
            features = processor(waveform, sampling_rate=16000, padding=True, return_tensors="pt")
            input_features = features.input_features.to("cuda")
            attention_mask = features.attention_mask.to("cuda")

            gen_tokens = model.generate(input_ids=input_features, attention_mask=attention_mask)
            transcription = processor.batch_decode(gen_tokens, skip_special_tokens=True)

            print(transcription[0])

if __name__ == "__main__":
    transcribe(sys.argv[1], sys.argv[2], sys.argv[3])    
```

# Evaluation

## Appendix A: General Improvements

## Appendix B: Segments vs Speeches