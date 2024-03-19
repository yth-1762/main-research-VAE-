# main-research-VAE-

# 주제
- Augmentation data with VAE that increases classification performance for sparse category 

# 배경 & 프로젝트 목표
- 머신러닝 및 딥러닝 모델들은 분류에 있어 데이터의 개수가 작은 경우(예: 파산,금융사기)의 범주보다 데이터의 개수가 많은 범주를 맞추는 것에 훨씬 높은 정확성을 보인다. -> 데이터의 개수가 작은 범주의 낮은 정확도 문제 발생
- VAE에 예측모형(FFN)을 추가(기존 loss function에 예측 loss function(binary cross entropy)를 추가)하여 데이터의 개수가 작은 범주의 정확도를 높이는 데이터를 VAE가 생성하도록 한다.

# 일정
- 2022.09 - 2023.12


# 데이터
- GLM 모형에 적합한 가상 데이터 10만개 생성 후 train,test로 분류(8:2비율)[독립변수 10개, 종속변수 1개(0(데이터 개수가 많은 범주), 1(데이터 개수가 적은 번주)

# 사용언어/모델
- R/VAE

# 모델 성능 지표
- test data accuracy

# 데이터 전처리
- 범주 1의 데이터를 0.05% 비율로 down sampling을 한 다음 0의 범주 개수(4만개)만큼 oversampling을 한다.

# 기존 예측 모델 성능
- 아래와 같은 간단한 FFN 모형에 oversampling된 데이터를 fitting하여 test data의 accuracy를 살펴본다.(total accuracy: 70.815, accuracy for case 0: 76.10887, accuracy for case 1: 65.60516)
  ```
  network3 <- layer_dense(units = 1, activation = "sigmoid", input_shape = c(10)) 

  network3 %>% compile(
  optimizer = "rmsprop",
  loss = "binary_crossentropy",
  metrics = c("accuracy")
  )

# VAE 변형 모델 성능
- 

  
  









# 기대효과
- 지구 근처의 소행성들이 어떤 종류의 소행성인지 판별
- 소행성이 잠재적 충돌 위험이 있는지 판별



