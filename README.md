# Unreal-Engine-Lighting-Tips / 언리얼엔진 라이팅 구현 팁

* 라이팅 구현에 정답은 없음. 
---
주로 사용하는 것
- DirectionalLight _ 태양광 (stationary) *stationary : static과 movable을 합친 느낌

![1](https://github.com/kcasl/Unreal-Engine-Lighting-Tips/assets/93076513/f4eb962e-32ae-4889-b418-fb9c0eb0dc06)


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

Lightmass Portal : 빛이 들어올 입구에 크기를 맞추어 배치.
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

---
<h2>하늘(sky) 색감 조절</h2>

Exponential Height Fog의 Component 부분에 있는 Fog Inscattering Color을 조절한다.
R,G,B,A 각각 1 이상의 값도 설정 가능함.

- 빛을 반사하는 메시의 퀄리티를 올리고 싶은 경우
레벨에 SphereReflectionCapture을 배치하고 빌드.

---
<h2>라이팅 후 벽 또는 메시에 빛이 새어들어오는 경우</h2>

- 이는 라이트맵 해상도 때문에 발생하는 문제이다.

뷰포트의 보기 방식을 라이트맵 밀도로 바꾸어주면 볼 수 있다.

- 주로 많이 보게되는 부분의 라이트맵 해상도는 높은 편이고, 주로 보지 않는 부분은 낮은 편이다.
- 
- 만약 이 때문에 퀄리티가 낮아 보인다면, 해당 벽 메시의 Lighting 부분의 Overridden Light Map Res를 활성화하고(활성화 되어있으면 조절)

-> 값을 높여주면, 해상도가 높아지고 빛이 새어들어오는 문제가 해결된다.

---
<h2>완성도를 높이는 방법들</h2>

Exponential Height Fog의 Volumetric Fog를 활성화 한다. 

- Extinction Scale을 10정도로 세팅한 후 Scattering Distribution 값을 조절해 주면 햇살을 표현할 수 있지만 잘 보이지는 않는다.
- 이후 DirectionalLight의 Volumetric Scattering intensity값을 올려주면 햇살이 자연스럽게 보이게 된다.

---

PointLight를 활용할 수도 있다. 

- PointLight를 배치하고, intensity를 낮추고, cast shadow 옵션을 비활성화 해 그림자를 드리우지 않고, 색감만 조절하여 식물등에 반사 된 빛을 표현한다.

- Bake하지 않고 사용한다.

### 더 화려하게?

햇빛을 더 과하게 들여와 화려한 느낌을 주고싶다면, PostProcessVolume -> Lens의 Bloom 값을 조절한다.

- Intensity 항목을 활성화 하고 값을 조절한다.
- Threshold 값도 적절히 조정하면 된다. 


- Color Grading 부분에서 채도, 명도 등의 부분을 조정할 수 있다.
- Saturation, Contrast등을 조정하면 됨.

- -> 이 과정에서 어두워진 부분을 조금 밝게 하고 싶다면 Gamma와 Gain을 적절히 조정한다.

---
<h2>Cinematic?</h2>

레벨에 CineCameraActor을 배치한다.


-초점 맞추는 방법 : Manual Focus Distance의 스포이드를 뷰포트에 찍어 초점을 맞춘다.


- ++ width : 21, height : 9 정도
- Focal length 조절


- 카메라에 가까이 붙은 메시가 흐려지는 경우
- -> Current Aperture값을 조정


- +PostProcessVolume의 Cinematic Aberration 부분의 Intensity와 Start Offset을 조정하여 가장자리 왜곡효과를 줄 수도 있음.

  
- 화면 외곽부분을 어둡게 하고 싶은 경우
- ->Image Effect 항목의 Vignette Intensity 조절


- 세팅이 끝나고 화면을 더 선명하게 보고싶은 경우 콘솔 명령어를 적용하면 됨.
- 
 <pre><code>r.tonenapper.sharpen 숫자</code></pre>
 
-> 기본값은 0 / 2~3 정도만 해도 충분히 선명해짐.

