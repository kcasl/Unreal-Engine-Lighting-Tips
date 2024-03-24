# Unreal-Engine-Lighting-Tips / 언리얼엔진 라이팅 구현 팁

* 라이팅 구현에 정답은 없음. 
---
주로 사용하는 것
- DirectionalLight _ 태양광 (stationary)
* stationary : static과 movable

- SkyLight _ 환경광 (static)
- ExponentialHeghtFog _ 환경광 (static)

---
<h1>창문 하나 있는 방에서의 라이팅</h1>

방 안에 Preview라는 글자가 새겨지는 것
+ SkyLight와 DirectionalLight를 movable로 바꿔서 해결가능
+  방 안에 빛이 자연스럽게 들어오도록 DirectionalLight의 각도를 적절히 조절.

* 보는 각도에 따라 밝아졌다 어두워졌다 반복될 수 있음.
* -> Auto Exposure 체크 해제 후 작업
* 그 후 PostProcessVolume을 배치 후 Lens 항목의 Exposure Compenstation과 Min,Max EV100 값을 적절히 조정.

===
ㄴㄴ
