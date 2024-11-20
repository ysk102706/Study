빌보드는 주로 멀리있는 배경 오브젝트에 사용되며, 계속해서 시점을 바라보도록 Geometry Shader를 통해서 구현한다. 

빌보드의 forward, right 벡터를 구한 뒤, 이 벡터를 이용해서 중심점의 좌표를 받아서 사각형의 꼭짓점 좌표를 계산한 뒤, outStream에 넣어준다. 이때 출력 스트림을 TriangleStream으로 한다. 

