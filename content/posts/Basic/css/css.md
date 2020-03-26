---
title: 
date: 2020-03-14 23:56:00
tags:
---



# CSS 적용 방법
1. link 태그를 이용해서 별도로 작성된 CSS 파일을 임포트하여 적용
2. Html 파일에서 Style 태그에 적용 (Bad)
3. 실제 엘리먼트 요소에 바로 적용 (Bad)

# display 
## block

블록 -> 길막 ?

블록 : 사각형 덩어리 , 막다, 차단하다.

width 주지 않는다면 부모의 width 값을 따라감

* block으로 설정하면, width값을 뺸 나머지 공간을 margin으로 채움
* height의 경우 별도로 주어지지 않는다면 자식노드의 height들의 합이 부모의 height가 된다.


## Inline
width, height, padding, boder, margin ( top, bottom) 사용못함 

## Inline Block
Inline 과 Block의 장점을 둘다 사용할 수 있음




# Float
* Block을 가로배치 하기 위해 사용함
* 요소를 float를 하면 -> Block으로 바뀜
* Block이 되지만 Block의 특징은 일부임, 길막을 하는 특징을 잃어버림
-> 레이아웃이 망쳐짐
* 고치는 법
    * overflow : hidden
    * clearfix
    * Pseudo Element : 가상 fake 요소

