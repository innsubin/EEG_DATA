# EEG_DATA

목적 : Raw data로 입수된 뇌파가 푸리에 변환을 통해 delta, theta, alpha, beta, gamma 다섯 가지 주파수 각각의 대역별로 분리가 잘 되었는지 결과 비교

데이터셋 소개
- https://www.kaggle.com/datasets/wanghaohan/confused-eeg (EEG_data.csv : 12,811 row x 15 columns)
- 학생 10명, 비디오 2분 분량 10개 (총 100개 케이스 존재)
- 30초~1분30초 구간(1분) 뇌파 데이터 수집
- 데이터포인트 0.5초마다 샘플링 (60초 비디오를 0.5초마다 샘플링 => 120 row 이상)


과정 ①
- csv 데이터를 SubjectID와 VideoID로 필터링 후 각 feature의 값들을 딕셔너리 형태로 저장
- 'user-definedlabeln' 값이 0일 때 혼동되지 않음(집중O)과 1일 때 혼동됨(집중X)로 분류
- 각 주파수 대역별 성분의 정도를 비교하기 전 0.5초와 1초로 샘플링 했을 때의 원본 raw 데이터 파형 변화를 비교
- Case 1 > label : 0 - SubjectID : 0.0, VideoID : 3.0 / label : 1 - SubjectID : 0.0, VideoID : 2.0

- 1초로 샘플링한 경우 홀수 인덱스만 추출하였는데 누락된 데이터들로 인해 파형이 비슷하게 따라가지 못함 -> 0.5초 샘플링을 기준으로 진행
- label 0과 label 1의 raw 데이터를 푸리에 변환하고, 주파수 대역별로 나누기 위해 fft.fftfreq 함수를 사용하여 주파수 대역을 생성
- delta, theta, alpha, beta, gamma 다섯 가지 주파수의 범위 별로 주파수 성분을 추출하고 이를 시각화함

- 처음 각 라벨에 대한 1개의 케이스를 서로 비교했을 때 차이를 크게 확인하지 못하여 각각의 다른 케이스를 하나씩 추가하여 비교
- Case 2 > label : 0 - SubjectID : 0.0, VideoID : 7.0 / label : 1 - SubjectID : 0.0, VideoID : 8.0
- 하지만 집중 상태에서 주로 활성화되는 베타파와 간마파를 비교하였을 때, 차이점을 찾지 못함
- 데이터셋의 feature 중 집중 정도를 나타내는 'Attention' 값에 따라 새로 라벨링하여 비교분석


과정 ②
- 전체 데이터에 대해 'Attention' 값의 중앙값인 43.0을 기준으로 43.0 이상을 집중(label = 0), 43.0 미만을 집중안함(label = 1)로 분류
- label 별로 나눠 대역별 주파수 성분을 추출하고 시각화
- SubjectID : 0.0, VideoID : 2.0 / SubjectID : 1.0, VideoID : 4.0
- label = 0일 때의 베타값이 label = 1보다 좀 더 활성화된다고 보여짐


한계
해당 데이터셋에서 각 주파수 대역별 성분과 집중 정도를 나타내는 Attention 값의 상관관계를 찾지 못함
