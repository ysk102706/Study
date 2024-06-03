Graphics Pipeline(Rendering Pipeline)은 3 차원 공간을 2 차원 상에 투영하는 과정을 말하며, 총 7가지 과정으로 구성되어있다. 7가지 과정들은 Fixed Pipeline과  Programmable Pipeline로 구성되어 있으며, 모든 과정이 필수는 아니다.
##### Fixed Pipeline
- 정해진 연산만 수행하므로 프로그래머가 GPU연산에 관여할 수 없다.
##### Programmable Pipeline
- 프로그래머가 GPU연산에 직접 관여할 수 있다.

// 나중에 파이프라인 이미지 그려서 넣기

#### Input Assembler (Fixed Pipeline)
- Vertex Buffer에서 Vertex Data를 읽고, 해당 데이터들을 다른 단계에서 사용할 Primitive Data로 조립한다.
- Index Buffer를 이용하여 Vertex의 중복이나 복제를 막는다.
#### VertexShader (Programmable Pipeline)
- 항상 수행되어야 하는 단계로 모든 정점에 대해서 실행되며, 하나의 정점에 대해서는 한 번만 호출된다.
- Transformation, Skinning, Vertex Lighting 등의 작업을 수행할 수 있다.
- 정점의 좌표계 변환이 이루어진다.