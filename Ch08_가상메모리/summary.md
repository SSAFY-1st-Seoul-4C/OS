#8.가상 메모리 요약

###### 메모리 크기보다 큰 프로그램이 작동하는 이유는?
OS가 프로그램을 작은 조각으로 나누어 일부분만 메모리에 적재 + 비연속적으로 저장 -> 많은 프로그램 수용 가능 (가상 메모리)

## 8.1 가상 메모리를 위해서는
>  **Page** : 같은 크기로 자른 프로그램의 조각
>  **Segment** : 다른 크기로 자른 프로그램의 조각
>  **Block** : 메모리와 디스크 사이의 전송 단위(실체는 Segment 또는 Page)
>  **Paging** :Page를 Block으로 사요하는 가상 메모리 관리
>  **Segmentataion System** : Segment를 Block으로 사용하는 가상 메모리 관리

###### Memory Mapping(실주소와 가상 주소 변환)를 사용하는 이유는?
Virtual Address : 프로그램이 참조하는 주소
Real Address : 실제 메모리 주소
 
 프로그램을 조각을 내어서 충돌 없이 메모리를 활용하기 위해서
unless..

|| compile시 주소 지정 이루어짐 | 재배치 |
|-----|-------------|------------|
| 단점 | 두 주소가 같으면 충돌|컴파일시, 프로그램 통째로, 연속적으로 메모리에 적재되어야 함(융통성 無)  | 
| 장점 |단순함|메모리의 효율적인 이용, 융통성| 

###### 프로그램을 조각 내는 이유 : 큰 프로그램을 실행, 많은 프로그램을 실행
-> 어떻게 사용?
 - Memory에서는 다양한 프로그램의 조각이 비연속적으로
 - HDD에서 나머지가 각 프로그램 조각이 존재
 
###### 프로그램은 분할된 메모리를 어떻게 사용하는가?
 분할되어 비연속적으로 분포한 메모리를 프로그램이 직접 접근해서 사용하는 것은 복잡하다(프로그램이 실제 주소를 알아야 함). 따라서 순차적인 가상 주소를 프로그램이 사용하고 OS가 이를 실제 주소와 Mapping하는 기법을 사용한다.
 
- - -

- 가상 주소의 정보: 참조하고자 하는 명령어나 변수가 프로그램의 **"몇 번째 조각인지와 몇 번째 위치(offset, byte기준)"**을 알려준다

## 페이징(Paging, KB)
> Frame : 페이지와 같은 크기로 나누어져 있는 메모리를 나눈 단위

Process > Page 
Memory > Frame(kB) 

Mapping의 단위: 프로세스 중 디스크와 메모리를 오가며 교체되는 단위

### Map Table
- per process
- size $$$\propto$$$ # of pages ->  K 개 Entries(4B, K개 pages일 때)
- who divides pages? OS, process 실행 시
- saved in memory for process in active, while savied in disc for idle process
- updated when? proces 실행 도중 갱신이 있을 때마다

##### Entries
- Residence Bit : 1-on Memory(Frame number), 0-Not on Memory(Field Info, addr in Disc)
- page table depends on its current situation?
![referred](https://techdifferences.com/wp-content/uploads/2016/12/Paging.jpg)
Img reference : https://techdifferences.com/wp-content/uploads/2016/12/Paging.jpg 

##### Page Table의 문제
가상 메모리를 사용하지 않을 때에 비교하여 page table에 접근하고 실주소를 찾아 다시 메모리에 접근하기 때문에 2 배로 시간이 걸린다
-> 해결책 1. 매핑이 많은 테이블을 메모리에 저장
해결책 2. Associate Memory 사용(TLB)

### TLB(Translation Lookaside Buffer)
: 키 값으로 참고되는 워드를 찾는 검색에 빠른 캐시 하드웨어 -> 빈번하게 검색되는 엔트리를 저장하는 캐시 역할
Key : 페이지 번호
Value : 실제 주소 

![reffered](http://pages.cs.wisc.edu/~bart/537/lecturenotes/figures/s18.tlb.gif)


> hitratio : TLB에서의 검색 성공 확률

## Page 보호(Protection)과 공유(Sharing)

###### Protection
1) 접근 페이지에 대해 읽기/쓰기 작업 제한 : 엔트리의 보호 비트(Protection Bits)을 사용하여
2) 다른 프로세스의 주소 공간 침범하지 않도록 : offset의 크기 <= page의 크기이도록 트랩 섶치

###### Sharing

메모리 낭비를 방지하기 위해서 Sharing하게 되어 메모리를 공유하지만 paging table은 따로 만들어 사용한다.

> Reentrant/Pure Code(재진입코드): 두 프로세스가 공유하는 코드

cf) 페이지 : 코드 페이지(mapping 필요), 데이터 페이지(mapping 필요 없음)

### Map Table 실제적 구현 (계층구조)
필요성 : 테이블 의 크기가 4Mbyte가 될수도 있을만큼 너무 크다
-> Root Table(1024 Entry), Map Table

그림 8.5 참조!

or 역 페이지 테이블


## 추가
- 마지막 Paging 시 내부 단편화 발생(마지막 page가 모두 채워지지 않을 수 있음) 
어떻게 해결??

## Segmentation
###### 프로그램의 구성 요소
주 프로그램, 프로시저, 함수, 전역/지역 변수, 호출 시 사용되는 스택..
-> 논리적인 하나의 단위 = 세그먼트

: 세그먼트 크기로 mapping하는 기법

##### Mapping 과정
1. paging 과 비슷하나 엔트리의 정보 중 프레임 번호 대신 실주소가 적혀있음 (paging 떄는 frame 번호)
2. 엔트리에 세그먼트 길이가 적혀있는 필드 있음
3. 세그먼트를 정교하게 제어하는 Access Control Key를 가짐 

##### 특징
1. paging 의 마지막 page의 내부단편화 문제는 없다 (Fit 기법 활용). but 'hole'이라는 외부 단편화 발생 가능

##### 이점
1) sharing에서 이점
2) 논리적 단위로 나누기 때문에 단위별로 수정하기가 편하다! 

### Protection and Sharing
protection은 segmentaion된 메모리를 사용할 때, 올바른 용도로 사용되도록 entry의 접근 제어 키를 사용하여 세그먼트 별로 허용되는 작업을 제어한다.

segmetation의 이점은 sharing에서 나타난다. 페이징의 경우, 페이지 크기가 다르면 공유할 필요 없는 부분이 공유 될 수도 있고 각 프로세스의 테이블의 공유하고자 하는 엔트리 위치가 같아야 한다. 
(coz 페이지 맵은 실주소가 아니라 frame number로 변환함)

## Paging을 사용하는 Segmentation
segment 가 page로 다시 나누어짐








 
