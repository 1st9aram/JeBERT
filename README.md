# jebert
Groom x K-digital training으로 진행된 "AI기술 자연어 처리 전문가 양성 과정 3기" 3조 프로젝트. 해당 과정은 Google Colab Pro+에서 진행했다. 

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

#### Dataset
* JIT 
* AI-HUB : 아래아가

JIT는 Kakao에서 제주어를 보존하기 위해 만든 데이터셋. 아래아, 쌍아래아가 포함된 *제주-한국 병렬 말뭉치*. 
AI-HUB 데이터는 아래아가 포함되지 않은 제주-한국 병렬 말뭉치. 개발환경과 시간의 한계로 총 200만개의 데이터 중 20만개를 사용했다. 

|data|제주어|표준어|
|:--:|:--:|:--:|
|JIT|![image](https://user-images.githubusercontent.com/70511222/168055112-91ad31ac-7c1f-4720-a586-3b34f589578e.png)|![image](https://user-images.githubusercontent.com/70511222/168055173-ea65dbd2-987b-4300-9cfb-1f80b151d8ff.png)|
|AI-HUB|혼저옵서예|어서오세요|


모델에 사용한 데이터는 JIT 17만 개, AI-HUB 데이터 20만개를 합쳐 총 37만개이다. 이 중 학습에 36만개, 검증에 5,000개 시험에 5,000개를 사용했다. 

#### 아래아 번역기
많은 데이터를 확보하기 위해 AI-HUB에서 제공하는 json 데이터를 활용하고자 했다. 

## huggingface link
https://huggingface.co/kompactss

## reference 
https://github.com/monologg/KoBERT-Transformers
https://github.com/kakaobrain/jejueo/blob/master/translation/bpe_segment.py
