# JeBERT
Groom x K-digital training으로 진행된 "AI기술 자연어 처리 전문가 양성 과정 3기" 3조 프로젝트. 해당 과정은 Google Colab Pro+에서 진행했다. 

- [JeBERT](#JeBERT)
  - [Purpose](#purpose)
  - [Requirements](#requirements)
- [프로젝트 진행](#프로젝트-진행)
  - [Dataset](#Dataset)
  - [아래아 번역기](#아래아-번역기)
  - [Vocab](#Vocab)
  - [Model](#Model)
  - [Metric](#Metric)
  - [Performance](#Performance)

---

## Purpose
제주어는 한글의 제작 원리를 보여주는 언어로, 현대에는 거의 사용되지 않는 아래아나 쌍아래아를 여전히 사용하고 있다. 이런 점에서 특수성과 언어학적 가치를 인정받고 있다. 그러나 최근에는 노령인구만 드물게 사용하는 "치명적 위험" 상태로 언어소멸의 위험에 처해있다. 뛰어난 역사적 가치를 지는 제주어를 보존하고 많은 사람들의 관심을 유발하기 위해 *표준어-제주어 번역기*를 개발하였다. 



### Requirements
- python3 == 3.7.13
- torch == 1.11.0
- transformers == 4.18.0
- sentencepiece == 0.1.96
- pyngrok == 5.1.0
- streamlit == 1.11.0

---
## 프로그램 진행

### Dataset
* [JIT](https://www.kaggle.com/datasets/bryanpark/jit-dataset) 
* [AI-HUB](https://aihub.or.kr/aidata/33982) 

JIT는 Kakao에서 제주어를 보존하기 위해 만든 데이터셋. 아래아, 쌍아래아가 포함된 *제주-한국 병렬 말뭉치*. 
AI-HUB 데이터는 아래아가 포함되지 않은 제주-한국 병렬 말뭉치. 개발환경과 시간의 한계로 총 200만개의 데이터 중 20만개를 사용했다. 모델에 사용한 데이터는 JIT 17만 개, AI-HUB 데이터 20만개를 합쳐 총 37만개이다. 이 중 학습에 36만개, 검증에 5,000개 시험에 5,000개를 사용했다. 

|data|제주어|표준어|
|:--:|:--:|:--:|
|JIT|![image](https://user-images.githubusercontent.com/70511222/168055112-91ad31ac-7c1f-4720-a586-3b34f589578e.png)|![image](https://user-images.githubusercontent.com/70511222/168055173-ea65dbd2-987b-4300-9cfb-1f80b151d8ff.png)|
|AI-HUB|혼저옵서예|어서오세요|


---

### 아래아 번역기 
아래아 번역기는 아래아가 표기되지 않은 AI-HUB 데이터를 JIT와 동일하게 아래아를 포함하도록 변환해주는 번역기로 동일한 형태의 데이터를 모델이 input으로 받도록 전처리 과정에서 사용된다. 

많은 데이터를 확보하기 위해 AI-HUB에서 제공하는 json 데이터를 활용하고자 했다. 이 데이터는 JIT와 달리 아래아를 표기하지 않고 해당 부분을 발음대로 표기하여 같은 단어도 다르게 표현되는 경우가 있다. 
![image](https://user-images.githubusercontent.com/70511222/168066765-eaf87c9f-c3e5-42a3-9194-24d2ea7f4b1a.png)

따라서 JIT의 아래아를 ㅗ, ㅏ, ㅓ로 변환한 데이터를 input,  원본 JIT를 target으로 하는 번역기를 생성했다. 이 번역기를 AI-HUB데이터에 적용, 아래아를 추가했다. 아래아는 현재 ㅏ, ㅗ, ㅓ로 발음되고 있어 번역기는 아래 두 가지를 만들어 사용했다. 해당 번역기로 전처리된 AI-HUB 데이터를 각각 Preprocess AI-HUB와 Preprocesse AI-HUB-v2로 표기하겠다.
* input(아래아를 "ㅗ"로 변환한 JIT) -> 원본 JIT
* input(아래아를 "ㅏ","ㅗ","ㅓ"로 변환한 JIT) -> 원본 JIT  
![image](https://user-images.githubusercontent.com/70511222/168067977-a4ceca4c-a0e3-44aa-a660-08666931d14e.png)

---

### Vocab
WordPiece Tokenizer를 활용해 사전을 생성했다. 
* vocab size: 30522
* 사용 데이터: JIT + 아래아가 추가된 AI-HUB 데이터.

---

### Model
huggingface에서 제공하는 EncoderDecoderModel 사용을 사용했다. encoder와 decoder는 BERT의 모델 구조를 가져와 새로 학습을 진행했다. 
* Encoder: Transformer config(BertConfig)
* Decoder: Transformer config(BertConfig)

![image](https://user-images.githubusercontent.com/70511222/168069946-d71435ae-98be-4a24-b887-881bff69616b.png)

---

### Metric
평가 기준은 번역결과와 정답이 얼마나 유사한지 측정하는 BLEU를 선택했다. 높을수록 정답과 유사함을 나타낸다. 

![image](https://user-images.githubusercontent.com/70511222/168070564-dbc261f3-b8b5-4352-8723-53ca46f9bb36.png)

---
### Performance
모델 개선에 치중한 다른 논문과 달리 데이터 전처리에 집중한 우리 번역기의 성능이 더 높게 나온다. 

* BLEU score 비교

|모델|제주어 -> 표준어|표준어 -> 제주어|
|:--:|:--:|:--:|
|다른 논문(수정)|67.94|44.19|
|JIT|71.1|57.9|
|JIT + AI-HUB|73.7|57.8|
|JIT + preprocessed AI-HUB|**79.1**|67.3|
|JIT + preprocessed AI-HUB-v2|79.0|**67.6**|


---

### Demo
streamlit을 이용하여 실제로 번역기가 어떻게 동작하는지 확인할 수 있도록 했다. 

![녹화_2022_05_12_21_40_19_699](https://user-images.githubusercontent.com/70511222/168076970-acb41b98-8bfe-475e-843e-60e6f0aed21d.gif)



---

## huggingface link
완성된 번역기를 huggingface에 업로드 했으므로 아래 링크에서 다운받아 사용할 수 있다. 

https://huggingface.co/kompactss

## reference 
https://github.com/monologg/KoBERT-Transformers
https://github.com/kakaobrain/jejueo/blob/master/translation
https://arxiv.org/pdf/1911.12071.pdf
https://www.koreascience.or.kr/article/CFKO202130060729836.pdf
https://www.koreascience.or.kr/article/CFKO201612470014629.pdf

