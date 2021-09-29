# MLDL_Telecobiz_embedding

[ Telco biz. Embedding 기반의 SOP Classifier ]
  1. Upstream Task - Telco biz. Embedding 모델 구축
  2. Downstream Task - Embedding을 활용한 SOP Classifier 모델 구축




[ 상세 설명 ]
1. Upstream Task - Telco biz. Embedding 모델 구축
   1) 말뭉치 수집
      - SOP, SOR, JIRA 요청서 수집 (문서 단위 약 547,197건)
        문서 : 여러 개의 문장으로 이루어진 글
      - 약 2년치 데이터 (2019년 1월 말 ~ 2021년 6월)
      - sop_merged.xlsx, sor.xlsx, jira_xlsx
   2) Raw 데이터 정제 
      a. 1)번에서 수집한 말뭉치들을 1개의 파일로 merge
      b. merge한 데이터 대상으로 1줄에 1문장씩 올 수 있도록 문장분리 
      c. 특수문자, 이메일, 휴대폰 번호 등 제거할 문자열 정의 및 제거 
      d. 공백 문자 압축 처리 
   3) Tokenize 적용
      a. 형태소 분리 API 활용하여 정제한 말뭉치에 형태소 분리 적용 (Mecab 활용)
   4) Embedding 적용
      a. Word2Vec / FastText Model을 활용하여 각 Embedding 모델 생성



2. Downstream Task - Embedding을 활용한 SOP Classifier 모델 구축 
   1) 분류 대상 데이터 수집
      - SOP DATA (sop_dataset.xlsx)
   2) Raw 데이터 정제
      a. Raw 데이터에서 필요한 데이터만 추출 
         - 문의내용, 처리부서 정보 추출
         - Sentence(X) : 문의내용
         - Label(Y) : 처리부서
      b. Label(Y)값 정제
         - 동일 의미의 Label이나 오타나 띄어쓰기, 부서명칭 변경으로 인해 Label값이 다르게 되어 있는 경우 1개의 Label로 통합 
           ex) 영업팀, 영업1팀, 영업2팀, 영업 1 팀, 영어 2티 -> 영업팀으로 merge & cleansing 처리 
         - Cleansing 후 Label 개수 : 37건
      c. Label(Y)값 Labeling
         - LabelEncoder로 Labeling 적용
      d. Sentence(X)값 정제 
         - Upstream Task에서 말뭉치 정제했던 과정 동일하게 적용하여 정제처리
           단, corpus에서는 1줄 1문장이었다면, 여기서는 1줄 1문서로 정제 처리
      e. Tokenize 적용
         - Word2Vec, FastText Embedding을 사용할 경우 동일하게 Mecab으로 Tokenize를 적용하여 형태소 분리 적용
         - KoBERT 모델을 사용하여 Classifier모델을 만들 경우 BERTSPTokenizer를 적용하여 형태소 분리 적용
      f. Sentence(X)값 Labeling 적용
         - Word2Vec, FastText Embedding을 사용할 경우 1번 Upstream Task시 만들었던 Embedding 모델 활용하여 Labeling 적용
         - KoBERT 모델을 사용하여 Classifier모델을 만들 경우 KoBERT의 자제적인 Vocabr과 Embedding정보를 활용하면 되므로 Labeling 미적용
    3) 데이터 분리 
       - Train, Valid, Test 데이터 분리 (8:2)
    4) Classifier 모델 생성
       a. Word2Vec, FastText 
          - Tensorflow
          - Word2Vec, FastText은 동일한 Classifier를 사용하고 Embedding만 각각 다르게 적용
          - Classifier 모델 구성 
            Embedding -> Bi-LSTM(128) -> Attention(128) -> FC(128, 'ReLu') -> Dropout(0.3) -> FC(37, softmax)
       b. KoBERT
          - Pytorch
          - KoBERT 
         
            
