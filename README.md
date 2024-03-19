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
- 예측 모형 FFN이 포함된 VAE 모델을 다음과 같이 구성
```
# encoder
original_input_size = c(k+1)
inp <- layer_input(shape = original_input_size)
x <- layer_lambda(inp, f=function(x) {x[,2:(k+1)]})
y <- layer_lambda(inp, f=function(x) {x[,1:1]})
hidden_1 <- layer_dense(x, units=intermediate_dim, activation="relu")
dropout_1 <- layer_dropout(hidden_1, rate = 0.5)
hidden_2 <- layer_dense(dropout_1, units=intermediate_dim, activation="relu")
dropout_2 <- layer_dropout(hidden_2, rate = 0.5)
z_mean = layer_dense(dropout_2, units = latent_dim)
z_log_var <- layer_dense(hidden_2, units = latent_dim)
# sampling part
sampling <- function(args) {
z_mean <- args[, 1:(latent_dim)]
z_log_var <- args[, (latent_dim + 1):(2 * latent_dim)]
epsilon <- k_random_normal(
shape = c(k_shape(z_mean)[[1]]),
mean = 0.,
stddev = epsilon_std
)
z_mean + k_exp(z_log_var) * epsilon
}
z <- layer_concatenate(list(z_mean, z_log_var)) %>% layer_lambda(sampling)
# decoder + prediction model
output_shape = c(vae_batch_size, k)
decoder_hidden = layer_dense(units=intermediate_dim, activation="relu")
decoder_upsample = layer_dense(units = intermediate_dim, activation="relu")
decoder_reshape <- layer_reshape(target_shape = intermediate_dim)
decoder_hidden1 = layer_dense(units=k, activation="sigmoid")
pred_layer = layer_dense(units = 1, activation = "sigmoid")
hidden_decoded = decoder_hidden(z)
up_decoded = decoder_upsample(hidden_decoded)
reshape_decoded <- decoder_reshape(up_decoded)
hidden1_decoded = decoder_hidden1(reshape_decoded)
y_pred =pred_layer(hidden1_decoded)
vae_loss <- function(y, y_pred) {
x <- k_flatten(x)
x_decoded_mean_squash <- k_flatten(hidden1_decoded)
xent_loss <- 1.0 * # initial weight = 1
loss_mean_squared_error(x, x_decoded_mean_squash) # loss_categorical_crossentropy도 시도해 볼 것
kl_loss <- -0.5 * k_mean(1 + z_log_var - k_square(z_mean) - # initial weight = -0.5
k_exp(z_log_var), axis = -1L)
p_loss <- 1.0 * loss_binary_crossentropy(y, y_pred) # initial weight = 0 * 12000
k_mean(xent_loss*vae_w1 + kl_loss*vae_w2 + p_loss*vae_w3)
}
vae <- keras_model(inp, y_pred)
optimizers <- keras::keras$optimizers
vae %>% compile(optimizer = optimizers$legacy$RMSprop(learning_rate=0.0001), loss = vae_loss,
metrics = c("accuracy"))
# summary(vae)
## encoder: model to project inputs on the latent space
# encoder <- keras_model(inp, list(z_mean, z_log_var))
## build a digit generator that can sample from the learned distribution
# gen_decoder_input <- layer_input(shape = latent_dim)
# gen_hidden_decoded <- decoder_hidden(gen_decoder_input)
# gen_up_decoded <- decoder_upsample(gen_hidden_decoded)
# gen_hidden1_decoded <- decoder_hidden1(gen_up_decoded)
# generator <- keras_model(gen_decoder_input, gen_hidden1_decoded)
vae1 <- keras_model(inp, hidden1_decoded) # can be used for generating synthetic samples for case 0 and 1
# summary(vae1)
```
- 이 모형에 oversampling data를 fitting 시키고 VAE로 새로운 데이터를 생성
- 생성된 데이터의 범주를 모두 1의 범주라고 가정
- 이 데이터의 크기에 맞춰서 0의 범주의 데이터도 새로 생성된 1의 범주 데이터와 똑같이 개수를 맞추어서 준비
- 총 데이터(기존의 oversampling data + 새로 생성된 1의 범주 데이터 + 새로 생성된 1의 범주 데이터 개수에 맞추어서 새로 준비된 0의 범주 데이터)
- 총 데이터를 기존 FFN 모델에 fitting( total accuracy: 73.21, accuracy for case 0: 71.03842, accuracy for case 1: 75.37985) -> 전체적으로 accuracy 상승


# 기대효과 & 향후 계획
- 가상 데이터 적용 결과 데이터 개수가 적은 범주의  정확도가 상승한 것을 토대로 실제 한 범주의 개수가 적은 파산, 금융사기 데이터에 적용하여 성능이 좋아지도록 모델의 parameter를 변경한다. 연구결과를 통해 한국통계학회에 논문을 투고 하는 것을 목표로 한다.



