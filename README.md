### [AI기반 CCTV 영상분석을 통한 실시간 도시침수 조기감지 및 서비스 기획]

#### 💧 침수 피해를 미연에 방지하여 방지하여 도시침수 피해를 축소할 수 있는 조기 감지 서비스를 구축하는 프로젝트입니다.

<img width="853" alt="스크린샷 2023-04-15 오후 7 37 10" src="https://user-images.githubusercontent.com/106679267/232209097-e826aa23-979e-41a8-80f4-f06818beae8f.png"><br></br>

### 💧 목차
***
* [Insight](#insight)<br>
  * [Why](#why)<br>
  * [Insight](#insight)<br>
* [How](#How)<br>
  * [Data & Targeting](#data-&-targeting)
  * [Object Detection](#object-detection)
  * [Segmentation](#segmentation)
  * [Prediction](#prediction)
* [POC](#POC)<br>
* [Benefit](#Benefit)<br></br>

### 💧 Insight

#### ▪️ Why
***
2015년부터 2020년까지의 폭우 현황을 나타낸 그래프입니다.<br>
해가 지날수록 인명피해가 증가하고, 이에 따른 복구비용과 피해비용 또한 꾸준히 증가하는 추세입니다.<br>
특히 2020년에는 인명피해와 복구비용, 피해비용이 모두 전년도들에 비해 최고점을 찍었습니다.<br></br>
__거의 매해 반복되는 이러한 피해를 줄이기 위해 도시침수 범위를 감지할 수 있는 분석기법의 도입은 꼭 필요하다고 판단하였습니다.__<br></br>
<img width="850" alt="스크린샷 2023-04-16 오후 4 09 06" src="https://user-images.githubusercontent.com/106679267/232279308-7b5d6813-40c9-4256-88b2-844793952323.png"><br></br>

#### ▪️ Insight
***
먼저 **object detection**, 즉 물이 없는 상황에서 물을 감지하고 사람이 물에 빠졌을때 그 상황을 감지할 수 있으며,<br>
**segmentation**을 통해 물이 있는 상황에서 물의 증가를 감지하고,<br>
마지막 **prediction**을 통해 침수피해를 미연에 방지하여 도시침수 피해를 축소할 수 있는 조기 감지 서비스를 구축할 수 있도록 하였습니다.<br></br>
<img width="969" alt="스크린샷 2023-04-16 오후 5 44 19" src="https://user-images.githubusercontent.com/106679267/232287388-da391206-e27c-4a2a-a16a-f956911a35b4.png"><br></br>

### 💧 How

#### ▪️ Data & Targeting
***
**kaggle의 침수데이터 3000여 장과 용인, 금북, 죽전 지역의 1분당 스크린샷,용인 지역 1500여 개의 분당 기상 데이터를 통해 데이터를 구축**하였습니다.<br></br>
데이터 수집 과정에서 지정 지역인 **금북과 용인에서 실제 침수가 발생**하였고,<br>
확실한 침수 데이터를 확보함에 따라 침수가 발생한 세월교, 금북교 인근을 타겟팅하여 서비스를 구축하고<br>
이의 결과가 좋을 시 전국으로 확대하는 서비스를 구축할 것으로 기대하였습니다.<br></br>
<img width="969" alt="스크린샷 2023-04-16 오후 5 30 35" src="https://user-images.githubusercontent.com/106679267/232286697-d4c07934-fbe1-4b1d-8475-dd6f75153f56.png"><br></br>

#### ▪️ Object Detection
***
대상의 일반적인 특징을 학습하기 때문에 다른 영역으로의 확장에서도 뛰어난 성능을 보임에 따라 YOLO모델을 object detection에 활용하였습니다.<br>
v5m모델, v7x모델, v7모델을 사용하여 object detection을 진행하였으나, yolov5모델들은 물이 아닌 부분을 물로 감지하는 모습을 보였으며,<br>
yolov7의 mAP가 yolov5의 경우보다 높은것을 확인하였습니다.<br></br>
**따라서 여러개의 모델들 중 최종적으로 성능이 가장 좋았던 v7모델을 detection에 사용하였습니다.**<br></br>
<img width="968" alt="스크린샷 2023-04-16 오후 5 55 12" src="https://user-images.githubusercontent.com/106679267/232287961-6a027cc5-ec54-4b4b-a558-698617544a8c.png"><br></br>

#### ▪️ Segmentation
***
Detectron2는 모듈화가 잘 되어있어 여러 기능을 지원하며, 직접 만든 데이터셋을 모델에 쉽게 적용해볼 수 있습니다.<br>
금북교 사진 200여 장으로 테스트를 진행해보았을 때, 좋은 결과를 가져올 수 있었습니다.<br>
이후 CCTV영상에 적용시켜보았을 때 역시, 좋은 결과를 낼 수 있었으며, 이러한 성과들을 종합해본 후,<br></br>
**segmentation을 detectron2로 하기로 결정하고 진행하였습니다.**<br></br>
<img width="966" alt="스크린샷 2023-04-16 오후 6 03 47" src="https://user-images.githubusercontent.com/106679267/232288337-08a8f9b3-d03d-47f3-819c-f3b779ab8c34.png"><br></br>

#### ▪️ Prediction
***
segmentation을 통해 도출한 물 면적을 활용하여 종속변수를 생성하였습니다.<br>
또한 최적의 독립변수를 추출하기 위해 p-value와 다중공선성을 실시하여, p는 0.05보다 작고, 다중공선성은 10보다 작은 최종 독립변수 7열을 선정하였습니다.<br>
앙상블 모델로 채택한 보팅 리그레서 성능의 r2스코어는 약 0.967이상을 달성하여, 최적 모델로 보팅 리그레서를 선정하였습니다.<br></br>
<img width="966" alt="스크린샷 2023-04-16 오후 6 57 18" src="https://user-images.githubusercontent.com/106679267/232291538-f25fd588-30f7-462a-b543-19211f68cc18.png"><br></br>

침수단계를 3단계로 분할하여 일반은 0, 주의는 1, 침수는 2로 잡고 진행하였으며,<br>
각 구간의 픽셀임계값을 일반에서 주의는 252.8, 주의에서 침수는 357.4로 설정하였습니다.<br>
이후 precision, recall, f1-score가 대부분 0.9이상의 정확도를 가지는 것을 확인할 수 있었습니다.<br>
x축을 prediction, y축을 실제 값으로 산점도를 시각화하였으며, 그에 따른 임계값 또한 시각화에 설정하였습니다.<br></br>
**이를 통해 분류기준에 맞게 큰 이상없이 잘 분류된 것을 확인할 수 있습니다.**<br></br>
<img width="965" alt="스크린샷 2023-04-16 오후 7 00 23" src="https://user-images.githubusercontent.com/106679267/232291672-c37e91a2-3104-4ae4-bef7-4d80bdb3fdc4.png"><br></br>














