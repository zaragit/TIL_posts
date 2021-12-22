---
title: React Native - Flex 레이아웃 기초
date: "2021. 12. 22"
summary: React Native에서 flex를 이용한 레이아웃 구성 기초
---

### Flex
사용가능한 영역 내에서 해당 노드가 **채워지는 비율** 값

```JSX
const Flex = () => {
  return (
    <View style={{flex: 1, padding: 20}}>
      <View style={{ flex: 1, backgroundColor: "red" }} />
      <View style={{ flex: 2, backgroundColor: "darkorange" }} /> // red의 2배 크기
      <View style={{ flex: 3, backgroundColor: "yellow" }} /> // red의 3배 크기
    </View>
  );
};
```

<br>

### Flex Direction
flexDirection은 자식 노드들이 **배치되는 방향**을 제어

#### 속성
- column (default)
- row
- column-reverse
- row-reverse

![[flexDirection.png]]

<br>

### Justify Content
flexDirection와 동일한 방향에서 자식 노드의 **정렬** 방법

#### 속성
- flex-start (default)
	-> 시작부분 부터 나란히 정렬
	![[flex-start.png]]
- flex-end
	-> 끝부분 부터 나란히 정렬
	![[flex-end.png]]
- center
	-> 가운데 정렬
	![[center.png]]
- space-between
	-> 일정한 간격을 두고 정렬
	![[space-between.png]]
- space-around
	-> 일정한 간격을 두고 정렬 (padding과 유사)
	![[space-around.png]]
- space-evenly
	-> 모든 노드의 앞 뒤 여백이 동일하게 간격을 두고 정렬
	![[space-evenly.png]]
	
	
### Align Items
flexDirection과 **교차되는 방향의 정렬** 방법

#### 속성
- stretch (default)
	-> flexDirection이 'row'라면 height를 'column'이라면 width를 가능한 영역만큼 늘린다.
	-> width나 height을 지정한 경우는 동작 X
- flex-start
	-> 교차되는 방향의 시작 부분부터 정렬
- flex-end
	-> 교차되는 방향의 끝 부분부터 정렬
- center
	-> 교차되는 방향 가운데 정렬
- baseline
	-> 기준선을 두고 정렬 (자식노드의 fontSize가 다른 경우에 유용)
	![[baseline.png]]