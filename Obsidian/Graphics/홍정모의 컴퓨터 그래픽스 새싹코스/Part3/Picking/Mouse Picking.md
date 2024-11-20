마우스 좌표를 NDC 좌표계로 변환한 뒤, 해당 좌표의 Near Plane에서의 좌표와 Far Plane에서의 좌표를 World 좌표계로 변환하고, 이 좌표를 이용해서 Ray를 구성한다. 
Bounding Sphere와 Ray를 이용하여 충돌 지점을 구하는 것으로 구현할 수 있다. 
