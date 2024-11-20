기하 쉐이더는 Vertex Shader를 진행한 후에 정점을 추가로 생성하는 쉐이더이다. 

### 진입점 함수
```
[maxvertexcount(2)]
void main(point GSInput input[1], inout LineStream<NormalPSInput> outStream) 
{
    NormalPSInput output;
    outStream.Append(output);
}
```
- maxvertexcount는 필수 키워드로 생성하는 정점의 최대 개수를 정의한다. 
- GSInput은 VertexShader에서 넘어온 데이터이며, GSInput 앞의 point는 정점 묶음의 형태이다. 
	- point, line, triangle, lineadj, triangleadj를 사용할 수 있다. 
- input 옆의 [1]은 정점 묶음에 따른 정점 배열의 크기이다. 
- LineStream부분은 출력할 정점의 묶음 형태이다.
	- PointStream, LineStream, TriangleStream을 사용할 수 있다. 
	- <> 안의 데이터 타입을 outStream.Append(output) 형태로 outStream에 집어넣을 수 있다. 
	- outStream.RestartStrip() 으로 Stream을 초기화한다. 
- Geometry Shader는 Strip 형태로 출력한다. 