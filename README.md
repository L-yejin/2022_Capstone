### 2022_Capstone
# 안면 이미지 초해상화 & 특징점 검출
- 국민대학교 AI빅데이터융합경영학과 2022 캡스톤디자인 강의중 진행된 프로젝트
- 해당 주제로 [국민대학교 캡스톤 경진대회](https://github.com/L-yejin/2022_Capstone/tree/main/2022%20KMU%20%EC%BA%A1%EC%8A%A4%ED%86%A4%EB%94%94%EC%9E%90%EC%9D%B8%20%EA%B2%BD%EC%A7%84%EB%8C%80%ED%9A%8C) 장려상 수상
- 논문 작성 [‘저화질 안면 이미지의 화질 개선를 통한 안면 특징점 검출 모델의 성능 향상’](https://www.kci.go.kr/kciportal/ci/sereArticleSearch/ciSereArtiView.kci?sereArticleSearchBean.artiId=ART002972790) (한국지능정보시스템학회)

|기간|Tags|역할|
|--|--|--|
|2022.09.25~2023.05.30|CV, Super Resolution, Landmark Detection|팀원,2저자|

## 프로젝트 프로세스
1. main key 선행연구조사
2. task 제안
3. task관련 선행연구조사
4. task 확정
5. 데이터셋 구축
6. 모델 학습
7. 논문 작성

## 얻을 수 있었던 경험
1. 영어 논문을 포함한 선행연구조사 과정
2. 필요한 데이터셋 커스텀
3. 딥러닝 환경 구축
4. 필요한 모델 서칭
5. 딥러닝 모델 학습
6. 논문 작성

## 프로젝트 요약
### 화질 개선
- 본 프로젝트는 강의를 통해 main key에 대한 기초적인 학습을 한 후, 학생들의 자체적인 선행연구조사부터 딥러닝 모델 output 추출과 논문 작성까지 하는 end-to-end 프로젝트였다. 강의의 main key는 “detection”이었으며, 우리 팀은 다양한 환경에서의 안면 이미지에 대해 초해상화를 시킨 전후 그룹에 대한 특징점 검출 실험을 task로 정했다.
- 데이터셋은 AIHUB의 < 한국인 마스크 착용 얼굴 이미지 > 중 일부를 선정해 전처리 후 사용했다. 초해상화 모델로는 현재 안면 이미지 초해상화 SOTA 모델인 HifaceGAN을 사용해 downsampling, blur, noise 세 가지 화질 저하 요인에 대해 화질을 개선했다. 화질 개선 전 후 그룹에 대해 mlxtend, dlib이라는 오픈 소스 라이브러리를 사용해 특징점 검출을 진행했고, 화질 개선 후 그룹에서 검출 성능이 향상됨을 확인했다. 전체 흐름은 아래 그림과 같다.
![figure1](https://user-images.githubusercontent.com/104400282/236881302-735a2777-18cc-4505-b7c6-bedfba6f5f83.jpg)

## 실험 결과
- blur, noise, downsampling 세 가지 화질 저하 요인에 대해 화질 개선을 진행했고, 평가지표로는 FID, PSRN, SSIM, MS_SSIM을 사용했다. 이 중 FID 지표는 고차원인 이미지를 저차원 공간으 로 변경 후 공간상에서 분포의 거리를 구하는 것이 기본 컨셉이다. FID 식의 결과는 두 이미지의 분산의 거리에 비례하므로 값이 작을수록 실제 이미지에 가까운 이미지가 생성되었다는 뜻이다. 평가지표는 <Table 1>과 같다.
![image](https://user-images.githubusercontent.com/104400282/236881692-0c8b1bda-d400-40a1-a07e-8ca6cc6ea7e9.png)

- 학습량에 따른 화질 개선된 이미지 예시는 아래 <Figure 5>와 같다.
![image](https://user-images.githubusercontent.com/104400282/236881950-89cecae3-344e-4a2e-bdc5-0f12f2ae1e28.png)

### 특징점 검출
- 68개 안면 특징점 검출에는 오픈 소스 라이브러리인 mlxtend와 dlib의 face 68landmark detection 모델을 사용하고, 검출된 특징점을 평가한다. 안면 특징점 검출의 평가지표로는 일반적으로 NME(Normalized Mean Error)가 사용된다. 이는 이미지 상에서 안면이 차지하는 비율이 데이터마다 상이해 특징점의 좌표값 범위 또한 다르기 때문에 값의 정규화를 통해 표준화시킨 평가지표이다. 일반적으로 정규화 상수로는 두 눈 사이의 거리가 사용된다. 하지만 본 실험에 쓰인 이미지들은 이미 두 눈 사이의 거리를 통해 전처리를 거쳐 정규화를 할 필요가 없기 때문에 RMSE(Root Mean Square Error)를 사용했다. 또한 특징점이 검출되지 않은 경우, 모델의 출력값이 음수와 같이 이미지의 크기에서 벗어나는 좌표값을 갖고 있어 이를 그대로 평가지표에 반영하면 되지만, mlxtend와 dlib의 특징점 검출 모델은 오픈 소스 라이브러리의 모델이기 때문에 검출이 안된 경우 이 출력값을 알지 못한다. 따라서 본 실험에서는 각 모델별로 특징점을 검출해낸 비율과, 검출해낸 경우에 한해서 RMSE를 계산한다.
- 각 모델과 화질 저하 요인, 화질 개선 여부 별 특징점 검출 비율은 아래 <Figure 7>와 같다.
![image](https://user-images.githubusercontent.com/104400282/236882429-ae659f4e-32ec-4082-a865-e184d41de016.png)

- 특징점 검출이 된 이미지에 한해서 측정한 RMSE는 아래 <Figure 8>과 같다.
![image](https://user-images.githubusercontent.com/104400282/236882251-c46168e6-d4fd-4f4d-b2a3-383a164f579a.png)

