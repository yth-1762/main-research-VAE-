# main-research-VAE-

# 주제
- Augmentation data with VAE that increases classification performance for sparse category 

# 배경 & 프로젝트 목표
- NASA에 따르면 지구 근처에 있는 잠재적 충돌 위험이 있는 소행성들이 존재
- 지구 근처에 있는 소행성들의 종류(Apollo, Amor, Aten)를 판별 & 잠재적 충돌 위험성이 있는지 판별

# 일정
- 2023.04 - 2023.06

# 팀원수 & 맡은 역할
- 3명 / 데이터 전처리 및 보고서 작성
  

# 데이터
- NASA의 Near Earth Object Program의 Jet Propulsion Laboratory(California Institute of Technology) 연구소에서 수집된 데이터이고 총 12개의 변수를 가진 15619개의 데이터
1. Object.Classification (Y ): Orbit classification
2. Orbit.Axis..AU. (X1): Semi-major axis of the orbit in AU (궤도의 장반축,
AU)
3. Orbit.Eccentricity (X2): Eccentricity of the orbit (궤도의 이심률)
4. Orbit.Inclination..deg. (X3): Inclination of the orbit with respect to the eclip3
tic plane and the equinox of J2000 (J2000-Ecliptic) in degrees (황도면과 J2000
의 분점에 대한 궤도 경사, 도)
5. Perihelion.Argument..deg. (X4): Argument of perihelion (J2000-Ecliptic) in
degrees (근점 편각, 도)
6. Node.Longitude..deg. (X5): Longitude of the ascending node (J2000-Ecliptic)
in degrees (승교점 경도, 도)
7. Mean.Anomoly..deg. (X6): Mean anomoly at epoch in degrees (평균 근점 이
각, 도)
8. Perihelion.Distance..AU. (X7): Perihelion distance of the orbit in AU (궤도의
근일점 거리, AU)
9. Aphelion.Distance..AU. (X8): Aphelion distance of the orbit in AU (AU)(궤
도의 원일점 거리, AU)
10. Orbital.Period..yr. (X9): Orbital period in Julian years (율리우스년의 공전
주기, 년)
11. Minimum.Orbit.Intersection.Distance..AU. (X10): Minimum orbit intersection distance in AU (the minimum distance between the osculating orbits of the
NEO and the Earth) (최소 궤도 교차 거리(NEO와 지구의 진동 궤도 사이의 최소
거리), AU)
12. Asteroid.Magnitude (X11): Absolute V-magnitude (절대등급, V)
∗ AU(Astronomical Unit): 천문단위로 지구에서 태양까지 이르는 평균거리(1AU), 약 1억 5
천만km.


  

# 데이터 전처리
- 모든 수치형 독립변수의 단위의 통일화를 위해 표준화 처리
- 결측치는 Astroid.Magnitude(X11)변수에서 1개의 자료만 NA값으로 missing 되었는데, 총 15619개의 데이터 중 1개라 크게 영향을 미칠것 같지 않아 이에 대한 데이터는 삭제

# 사용언어/모델
- R/GLM

# 모델 성능 지표
- AIC, deviance

# 소행성 종류 분류 모델링 & 결과 해석
- 소행성의 종류를 예측하기 위해 baseline category logit model에 데이터를 적합(결과: Residual deviance는 1509.053, AIC는 1557.053 / Orbit.Eccentricity(X2), Perihelion.Distance..AU.(X7), Orbital.Period..yr.(X9)
총 3개의 변수가 유의수준 0.05하에서 유의확률 작게 나와 유의한 변수라 봄)
- ’VGAM’ 패키지 안에 있는’step4vglm’ 함수를 사용하여 변수 선택(AIC 기준)후(변수(X2, X7, X9)와 똑같은 변수가 선택) 다시 모델링 진행(결과: residual deviance는 1507.743, AIC는 1523.743,  Orbit.Eccentricity(X2)와 Perihelion.Distance..AU(X7)가 유의한 변수) -> residual, deviance 기준 더 적합한 모형
1)
log( ˆπ1/πˆ3) = 7.288+10.383×Orbit.Eccentricity(X2)+45.119×P erihelion.Distance..AU.(X7)
− 0.853 × Orbital.P eriod..yr(X9)
- X2가 1단위 증가할때 소행성군이 Aten보다 Amor일 오즈는 exp(10.383)배
와 같다.
- X7가 1단위 증가할 때 소행성군이 Aten보다 Amor일 오즈는 exp(45.119)
배와 같다.
- X9가 1단위 증가할 때 소행성군이 Aten보다 Amor일 오즈는 exp(0.835)배와
같다.
2)
log( ˆπ2/πˆ3) = 20.502+10.261×Orbit.Eccentricity(X2)+13.933×P erihelion.Distance..AU.(X7)
− 0.786 × Orbital.P eriod..yr(X9)
- X2가 1단위 증가할때 소행성군이 Aten보다 Amor일 오즈는 약 exp(10.261)
배와 같다.
- X7가 1단위 증가할 때 소행성군이 Aten보다 Amor일 오즈는 exp(13.933)
배와 같다.
- X9가 1단위 증가할 때 소행성군이 Aten보다 Amor일 오즈는 exp(0.786)배와
같다.
3)
log( ˆπ1/πˆ2) = −13.214+0.122×Orbit.Eccentricity(X2)+31.186×P erihelion.Distance..AU.(X7)
− 0.067 × Orbital.P eriod..y(X9)
- X2가 1단위 증가할때 소행성군이 Apollo보다 Amor 일 오즈는 exp(0.122)
배와 같다.
- X7가 1단위 증가할 때 소행성군이 Apollo보다 Amor일 오즈는 exp(31.186)
배와 같다.
- X9가 1단위 증가할 때 소행성군이 Apollo보다 Amor일 오즈는 exp(0.067)
배와 같다.

# 소행성 잠재 유무 판별 모델링 & 결과 해석
- 모델을 추정하기 위해 잠재적인 위험이 있으면 1, 없으면 0을 나타내는 새로운 Y변수 생성
- link function을 logit으로 설정하여 binomial glm model fitting(결과: Residual deviance는 3197.7, AIC는 3215.7 /  Minimum.Orbit.Intersection.Distance(X10), Asteroid.Magnitude(X11) 두 변수가 유의)
- R 내장 함수인 step function을 사용( AIC를 기준으로 변수 선택) orbit axis, Asteroid.Magnitude(X11),Minimum.Orbit.Intersection.Distance(X10) 변수로 다시 모델 fitting(결과: Null deviance: 11056.0, Residual deviance: 3191.7 on 15606 degrees of freedom, AIC: 3203.4)
- link function을 probit으로 설정하여 binomial glm model fitting(결과: Null deviance: 11056, Residual deviance: 3183, AIC: 3207)
- R 내장 함수인 step function을 사용( AIC를 기준으로 변수 선택) Orbit Axis..AU.(X1), Minimum.Orbit.Intersection.Distance..AU.(X10), Asteroid.Magnitude(X11) 변수로 다시 모델 fitting(결과: Null deviance: 11056.0, Residual deviance: 3187.3, AIC: 3195.3) -> deviance와 AIC 측면에서 가장 적합
- 해석:

- Orbit.Axis..AU(X1)가 한 단위 증가할 때마다 E(Y∗)는 0.06237만큼 감소한다.
  
- Asteroid.Magnitude(X11)가 한 단위 증가할 때마다 E(Y∗)는 2.33622만큼감소한다.
  
- Minimum.Orbit.Intersection.Distance..AU(X10)가 한 단위 증가할 때마다 E(Y∗)는 6.28228만큼 감소한다. 



# 기대효과
- 지구 근처의 소행성들이 어떤 종류의 소행성인지 판별
- 소행성이 잠재적 충돌 위험이 있는지 판별



