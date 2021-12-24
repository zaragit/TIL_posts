---
title: React Native - Animatied 기초
date: "2021. 12. 24"
summary: React Native에서 제공하는 Animated 객체 기초
---

## Animated
React Native에서는 애니메이션 구현을 위한 Animated 라이브러리 객체를 제공한다.   
Animated는 애니메이션에 사용할 값과 애니메이션을 실행을 제어하는 메소드를 제공한다.   

<br>

### Animated 컴포넌트
React Native의 Animated는 Animated component에서만 동작한다.   

#### Basic 컴포넌트
기본적으로 View, Text, Image, ScrollView, FlatList, SectionList 컴포넌트를 제공한다.    
- <Animated.View>
- <Animated.Text>
- <Animated.Image>
- <Animated.ScrollView>
- <Animated.FlatList>
- <Animated.SectionList>

#### createAnimatedComponent()
기본적으로 제공하능 컴포넌트에만 애니메이션을 적용할 수 있는 것은 아니다.   
Animated.createAnimatedComponent() 메소드를 사용하면 다른 컴포넌트에도 애니메이션을 적용할 수 있다.    
~~~jsx
import { Animated } from 'react-native';
import { Path } from 'react-native-svg';

/**
 * SVG Path를 Animated Component로 만든다.
 */
const AnimatedPath = Animated.createAnimatedComponent(Path);
~~~

<br>

### Animated 값
Animated values는 애니메이션으로 변환이 필요한 style properties나 그 외 props들을 선언한다.   
values 선언은 Animated.Value()나 Animated.ValueXY() 메소드를 사용하면 된다.   

<br>

### Animated 유형
Animated는 세 가지 유형의 애니메이션을 제공한다.   
애니메이션 유형은 Animated 값 초기 값에서 최종 값으로 변경되는 방식을 제어한다.   

#### 1. Animated.decay()
![[animated_decay.gif]]
[코드](https://github.com/zaragit/ReactNativeAnimationStudy/blob/master/src/Decay/DecayBasic.tsx)

점점 감속되는 느낌을 주는 애니메이션 유형   
솔직히 아직도 정확한 사용 방법은 모르겠다...    
내가 이해를 못 하는건지 공식문서를 아무리 찾아봐도 명황한 설명이나 예제를 찾을수가 없다.   
구글링해도 사용방법이 다르다. DecayAnimationConfig에는 없는 toValue 옵션을 사용하는데 당연히 TypeScript 에러가 난다. 나중에 시간있을 때 다시 확인 해봐야겠다.   

##### Options
- velocity (필수)
- useNativeDriver (필수)
- deceleration
- isInteraction

#### 2. Animated.spring()
![[animated_spring.gif]]
[코드](https://github.com/zaragit/ReactNativeAnimationStudy/blob/master/src/Spring/SpringBasic.tsx)

스프링이 튀는 것 같은 애니메이션 유형


##### Options
- toValue (필수)
- useNativeDriver (필수)
- overshootClamping
- restDisplacementThreshold
- restSpeedThreshold
- velocity
- bounciness
- speed
- tension
- friction
- stiffness
- mass
- damping
- delay


#### 3. Animated.timing()
![[animated_timing.gif]]
[코드](https://github.com/zaragit/ReactNativeAnimationStudy/blob/master/src/Timing/TimingBasic.tsx)

시간이 지남에 따라서 값이 변경되는 유형
가장 많이 사용한다.

##### options
- toValue (필수)
- useNativeDriver (필수)
- easing
- duration
- delay