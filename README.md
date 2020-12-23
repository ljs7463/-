# 프로젝트 설명
기존에 있는 펫케어 iot서비스를 보고 조금더 편하게 이용할 수 없을까?? 라는 생각에서 시작된 것 이며
반려동물의 주인이 모니터링 하지 않을 때에도 위험상황이라고 판단하여 사전에 지정해둔 이벤트를 발생시키는 것을 목표로 하는 프로젝트입니다
알고리즘은 욜로 알고리즘을 사용하였으며 이유는 Realtime 으로 실시간 처리를 하기위해서 사용하게 되었음






- [x]  darknet 을 이용하여 구현하기
- [ ]  opencv를 이용하여 no_gpu 상태에서 퍼포먼스 확인
- [ ]  darkflow를 이용하여 구현하기
- [ ]  darkflow를 통하여 메인파일에 접근하여 이벤트 발생 처리
- [ ]  웹캠과 연동하기 


블로그에도 간단하게 설명해 두었습니다.
블로그를 보려면[여기](https://blog.naver.com/ljs7463/222140569357 ) 를 눌러주세요




# Darknet 에서 구현 하는 방법 
### 1. 깃 허브 저장소에서 darknet 압축파일을 다운 받는다.
[여기](https://github.com/AlexeyAB/darknet) 를 눌러서 다운해 주세요

### 2. opencv/CUDA/CUDN 다운받는다
(오픈씨브이는 4.4.0다운후 파일명을 opencv_3.0 으로 바꾼다 이유는 원래 OPENCV_DIR은 환경변수로만 호출하게 해놨는데 요즘에는 저자가 하드코딩으로 OPENCV_3.0으로 같이 써놨기 때문이다, 귀찮게 환경변수 설정하고 재부팅 하는거 보다 편함)

### 3. 다운받은 darknet파일의 darknet.sln파일을 비쥬얼스튜디오에서 빌드한다 이때 속성에 들어가서 경로 설정 잘해줘야함.(OPENCV,CUDANCUDN등등)

### 4. 빌드에 성공하면 exe파일이 생성된 것을 볼 수 있다.

### 5. 훈련을 진행하기 위한 파라미터를 다운해야 하는데 그중 cfg파일을 먼저 수정해보았다.
(실제 순서는 상관이 없지만 나의 경우 이렇게 했다 정도로 알아주시면 됩니다.)
Yolov4-custom.cfg파일이 있는데 이것을 이름을 바꿔주는데 나의 경우yolo-obj.cfg
라고 이름을 바꾸어 줬다.

### 6. cfg파일을 열어서 안에 내용을 조금 수정해준다.
 - batch = 64 
 - subdivisions = 16 
 - max_batches = (클래스 수 * 2000)  #본인의 경우 한 개의 클래스를 학습 시키기 때문     에 
 - (1 * 2000) = 2000 을 넣어주었다. 
 - steps = max_batches 의 80% ,max_batches 의 90% #본인의 경우 1600, 1800
 - width, height = 각각 416 또는 32의 배수로 설정 
 - (ctrl + f) 누른후 검색바에 yolo검색 -> [yolo]아래에 있는 classes를 본인의 클래스 개수     입력
 - [yolo] 바로 위에 있는 [convolutional] 에 포함되어있는 filters 의개수를 (classes +5) *3
   #본인의 경우 (1+5)*3 = 18
#주의해야할 점 [YOLO]기준으로 바로 아래 classes수정과 바로 위 [convolutional]에 있는 filters 값 수정해야함!!

### 7. 그 외에 추가적으로 파라미터 파일들이 필요함
 - (6번에서 만든 yolo-obj.cfg)
 - obj.data
 - obj.names
 - train.txt
 
 이렇게 총 4가지가 필요한데 첫번째 cfg파일은 6번에서 완성 하였으므로 data파일을 먼저 만들어보자
 
 ## obj.data
 필자의 경우 data파일 생성시 깔려있는 vscode로 실행이 되게 해놨음 data는 확장자를 말함.
 파일을 만든후 내부에 이렇게 설정 하면 된다.
 classes= 1 
 train  = data/train.txt
 valid  = data/test.txt
 names = data/obj.names
 backup = backup/
 
 보시다 싶히 경로를 설정 한 파일이라고 생각하면 될듯 하다.
 첫 번째, 클래스의 개수를 의미하며 1개를 학습시키려고 하기 때문에 1을 적어주었다.
 두 번째, 나중에 학습시킬때 train 코드 입력시 연결되는 경로이다 data/train.txt 파일을 만들어 줄것이기 때문에 우선 이렇게 설정해 두면 된다. 앞 경로는 편하게 바꾸어도되지만 이경로가 편할거같다
 세 번째, 이것은 나중에 test 돌릴 때 필요한 코드를 저장한 텍스트 파일의 저장경로 이다. 마찬가지로 나중에 test.txt 파일을 만들것이기 때문에 이렇게 설정하면 된다.
 네 번째, 이 부분은 classe의 이름을 저장해둔 파일의 경로를 저장한 것이다.
 다섯 번째. 훈련을 시키고 가중치(weights)값이 저장되는 경로를 설정해둔 것이다.
 
 이렇게 까지 하면 obj.data 파일이 완성된 것이다.
 
 ## obj.names
 이 파일은 클래스를 학습시킬때 이름을 저장해두는 파일이다 마찬가지로 names파일로 저장하면된다.(워드패드)
 그리고 필자의 경우 강아지를 학습시키는 것이므로 dog라고 작성한후 파일을 닫았다.
 
 ## train.txt
 이 파일은 학습을 시킬때 그림과 바운딩박스 값의 경로를 적어둔 파일이다.
 나의 경우 이렇게 작성했었다.(이후 설명에서 이렇게 설정해둔 이유를 작성해 두었다)
 
 data/obj/dog1.jpg
 
 data/obj/dog2.jpg
 
 data/obj/dog3.jpg
 
        .
        .
        .
        .
 이렇게 하면 파라미터 값들의 세부적인 부분을 살펴 볼 차례이다.
 
 (참고로 필자는 파라미터 저장경로는  C:\darknet-master\darknet-master\build\darknet\x64\data 이렇게 data 폴더에 전부 저장을 해두었다)
 
 ### 8. 이미지 및 바운딩 박스 파일 생성 및 학습을 위한 작업

욜로마크를 다운 받기 위해서 [여기](https://github.com/AlexeyAB/Yolo_mark)를 눌러준다

마찬 가지로 visual studio에서 경로를 설정하고 빌드를 성공시켜 준다. 

그리고 링크에있는 방법대로 실행시켜서 바운딩 박스를 그리는 작업을 해준다.

이후 그림파일과 바운딩박스좌표를 나타낸 텍스트 파일을 복사를 한후

다시 다크넷 폴더로 돌아와서 x64/data 경로에 obj라는 이름으로 폴더를 만들어 준다. 

만든 폴더를 들어가보자. 경로는 x64/data/obj가 되어 있을것이다. 여기에 방금 복사해둔 파일들을 붙여넣기 해준다.

필자의 경우 각 사진의 파일명을 dog1, dog2, dog3 이런식으로 해두었다

### 9. train 준비하기

train 폴더에 들어가서  아까 위에서 말한것을 설명할 수 있게되었다. 위의  train.txt설명 부분에서 필자가 저렇게 텍스트를 설정한이유는

이 폴더에 자신의 사진의 경로를 적어야 하기때문이다. 따라서 필자는 그 사진들의 경로인 data/obj/dog1.jpg 를 적었던 것이다.

본인의 이미지 갯수에 따라 달라지겠지만 일일이 작성을 해야한다.

### 10. 가중치 값 받아오기

자신의 버전에 맞는 가중치 값을 받아오면 된다. darknet 홈페이지 또는 구글에서 검색하면 많이 나오기 때문이다.

또한 사용pc사양에 따라 tiny파일로 간단하게 사용해도 된다.

### 11. 학습 시켜보기

 - 1단계 : C:\darknet-master\darknet-master\build\darknet\x64 (사람마다 경로가 다르지만 x64폴더까지 이동후 경로를 다지우고 cmd 를 입력하            게 되면 이 경로로 바로 이동해 준다 )
 - 2단계 : 실행을 위한 코드를 작성해 준다 --> 
 ```python
 darknet.exe detector train data/obj.data data/yolo-obj.cfg data/(가중치파일명)
 ```
 
 - 3단계 : 엔터를 치면 학습이 시작된다.
 
### 12. 학습완료후 test 해보기

학습이 완료 되었다면 (필자의 경우 5시간 정도 걸렸다. gpu2060사용) test용 데이터가 필요하다 ( 학습할때 쓰지않은 사진자료, 필자의 경우 친구집
강아지 사진을 사용) 이 사진을 data 폴더안에 옮겨준다 (필자의 경우 .jpg 확장자로 저장)

다시 x64 위치에서 cmd창을 열어준다

코드를 입력한다 --> 
```python
darknet.exe detector test data/obj.data data/yolo-obj.cfg data/(가중치파일이름) data/obj/({사진파일이름}.jpg)
```


이렇게하면 test가 완료 됩니다!!

### 동영상 test

```python
darknet.exe detector demo data/obj.data data/yolo-obj.cfg data/(가중치파일이름) data/({동영상파일명}.mp4) 
```

이미지, 동영상 모두 확장자는 개인의 파일에 맞게 하시면 됩니다!!





# OpenCV에서 구현하는 방법
###### (기본데이터셋 이용)
