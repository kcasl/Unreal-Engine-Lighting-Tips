# Unreal-Engine-Lighting-Tips / 언리얼엔진 라이팅 구현 팁

* 라이팅 구현에 정답은 없음. 
---
주로 사용하는 것
- DirectionalLight _ 태양광 (stationary)
![1](https://github.com/kcasl/Unreal-Engine-Lighting-Tips/assets/93076513/f4eb962e-32ae-4889-b418-fb9c0eb0dc06)
*stationary : static과 movable을 합친 느낌

- SkyLight _ 환경광 (static)
![2](https://github.com/kcasl/Unreal-Engine-Lighting-Tips/assets/93076513/1e0ed6d6-4fdb-4fa0-af49-a58846b8c3cf)
- ExponentialHeghtFog _ 환경광 (static)
![3](https://github.com/kcasl/Unreal-Engine-Lighting-Tips/assets/93076513/f5b59c84-712e-49b9-b122-0826faae59cb)



---
<h1>창문 하나 있는 방에서의 라이팅</h1>

방 안이 보이지 않고 Preview라는 글자가 새겨지는 것
+ SkyLight와 DirectionalLight를 movable로 바꿔서 해결가능
+  방 안에 빛이 자연스럽게 들어오도록 DirectionalLight의 각도를 적절히 조절.

* 보는 각도에 따라 밝아졌다 어두워졌다 반복될 수 있음.
* -> Auto Exposure 체크 해제 후 작업
* 그 후 PostProcessVolume을 배치 후 Lens 항목의 Exposure Compenstation과 Min,Max EV100 값을 적절히 조정.

<h2>라이팅 환경 구성</h2>

스태틱 메시로 화면을 구성하고, 라이팅 환경을 구성한다.

---
// 환경
- Lightmass Importance volume
- Lightmass Portal
- Lightmass setting

// 색감 및 주 라이트
- DirectionalLight

// 하늘 및 배경 색
- Exponential Height Fog
---

Lightmass Portal : 빛이 들어올 입구에 크기를 맟추어 배치.
- 해당 부분을 통해 들어오는 라이트가 더 많이 계산됨. -> 더 자연스럽고 실제로 빛이 들어오는 느낌을 받을 수 있음.

Lightmass Importance volume : 이 Volume 안에서 라이팅 계산을 집중시켜주는 효과를 줌.
- 이 Volume 설치 후 World Setting에서 Indirect, Sky Lighting Bounces 등을 적절히 조정하면 됨 ex) 20

- Light Bake 준비 완료.
- SkyLight와 DirectionalLight를 다시 각각 static과 stationary로 바꿔주면 됨.
- 프리뷰 단계로 "라이팅만 빌드" 옵션으로 빌드.

---
<h2>전체적인 라이팅 퀄리티 조절</h2>
-> 빌드가 완료되면 방 안에 빛이 반사되어 들어와 밝아지게 됨.
but, 그림자의 해상도가 그리 좋지 못함.

-> 이 상태에서 DirectionalLight에서 Cascaded Shadow Maps를 활성화 시켜줌.
- Dynamic Shadow Distance Static의 값을 적절히 늘려주면 그림자가 선명해지는 것을 확인 할 수 있음.
- Dynamic Shadow Cascades의 값을 4정도로 늘려주면 그림자가 더 선명해짐.

빛이 얼룩져 보기 좋지 않은 경우. 
- World Setting -> Lightmass에서 Lighting Level Scale의 값을 낮추어 주면 됨.
*이 값이 낮아질수록 라이팅 빌드 퀄리티는 증가하지만, 빌드에 더 오랜 시간이 소요됨.

---
<h2>라이팅 색감 조절</h2>

DirectionalLight의 Use Temperature을 활성화 시키고, Temperature을 3500도 정도로 낮추면 빛이 따뜻한 느낌을 줌.

- 색 온도 표
![image](https://github.com/kcasl/Unreal-Engine-Lighting-Tips/assets/93076513/ac0a84b3-811a-498f-a6c5-a8b47c46a311)
reference : https://www.casadiluce.ca/blogs/how-to/guide-to-light-color-temperature





