# Chapter 8. 가상 메모리

[챕터 8 요약: summary.md](./summary.md)
[고정분할과 가변분할: 고정분할_가변분할.md, Oh,yes 7장!]

### 가상메모리가 무엇이며 그 필요성에 대하여 설명하시오
 많은 프로그램들을 Memory에서 수행하기 위해서 가상 메모리 기법을 사용
 ##### 어떤 원리로 가능한가?
 프로그램을 조각으로 나누어 일부만 메모리에 적재하는 것과 비연속적으로 저장하는 것으로 가능
 ##### 조각으로 나누면 memory에 없는 조각은 어디에 있나요? 그리고 조각으로 나누는 효과는?
 나머지 조각은 하드 디스크에 저장되어 있음. 
 메모리+$$$\alpha$$$(하드 디스크) 을 프로세스 적재 저장 장치로 사용하므로 적재할 수 있는 메모리의 양이 증가되어 더 많은 프로그램을 적재할 수 있는 효과가 있음
 
 ##### 비연속적으로 저장하는 것의 효과는?
 메모리 활용 효율성이 높아진다. 



### Paging을 구현하기 위해서 필요한 기술은 무엇인가요?

가상 주소와 실제 주소 간의 Mapping

 분할되어 비연속적으로 분포한 메모리를 프로그램이 직접 접근해서 사용하는 것은 복잡하다(프로그램이 실제 주소를 알아야 함). 따라서 순차적인 가상 주소를 프로그램이 사용하고 OS가 이를 **실제 주소와 Mapping하는 기법**을 사용한다. 바로 이것이 paging이다. 이때, 프로세스의 조각은 page라고 불리며 모두 같은 크기의 조각이다. 

 가상 주소를 실제 주소리로 변환할 때 참조할 때 사용되는 데이터 구조가 page table이다.  

cf) 조각의 크기가 다를 때는 segmentation([summary.md](./summary.md) 참조)

##### paging에 사용되는 mapping table은 어디에 저장되고 언제 만들어지는가? (segmetation도 동일)
 프로그램 생성할 때 OS에 의해 만들어지고 memory의 커널 영역에 저장된다.
 
 cf) memory의 커널 영역: 메모리 중 운영체제가 존재하는 영역이자 user level에서 직접 접근할 수 없는 메모리 영역
[mapping 과정: mapping_과정.md](./mapping_과정.md)

 
##### [TMI 문제] mapping table은 OS에 의하여 어떻게 관리되고 조회되는가?
 프로세스의 PCB에 각 table의 시작 주소값이 저장되어 있고 OS가 현재 실행하고 있는 table의 주소는 page table origin register에 저장된다. 그리고 변동이 있을 때마다 mapping table은 OS에 의해 갱신된다.


### 같은 프로그램을 여러 개 돌릴 때, 같은 Paging Table을 사용하는가? 왜?

메모리 낭비를 방지하기 위해서 frame은 Sharing하게 되어 메모리를 공유하지만 paging table은 process 마다 따로 만들어 사용한다.

##### 왜 Paging table은 따로 만들어 사용하는가?

### paging의 과정을 그려보시오.
![referred](https://techdifferences.com/wp-content/uploads/2016/12/Paging.jpg)

Img reference : https://techdifferences.com/wp-content/uploads/2016/12/Paging.jpg 


### mapping table의 크기가 4MB 로 매우 클 수 있는데 그럴 경우에는 어떻게 해야 하는가?
multi-level table을 사용하여 table 도 모두 메모리에 적재하지 않고 나눠서 적재한다.
또는 역(Inverted) 페이지 테이블을 사용하여 프레임의 크기 만큼 테이블 엔트리를 만들ㄴ다.

###### 역 table이란?
가상 주소의 페이지를 기준으로 table의 entry를 만드는 것이 아니라 frame 기준으로 page를 참조할 수 있게 entry를 만드는 것(같은 frame을 가진 page는 chaining)


### TLB가 무엇이고 이에 대해 설명하시오
[필요성] virtual memory를 사용하면 많은 프로그램을 실행시킬 수 있지만 table을 거쳐 메모리에 접근하기 때문에 메모리 접근 시간이 두 배이다.
해결책 1. 매핑이 자주 일어나는 테이블을 메모리에 저장 (테이블 또한 virtual memory에 저장)
해결책 2. Associate Memory 사용(TLB)

TLB(Translation Lookaside Buffer)
: 키 값으로 참고되는 워드를 찾는 검색에 빠른 캐시 하드웨어 -> 빈번하게 검색되는 엔트리를 저장하는 캐시 역할
Key : 페이지 번호
Value : 실제 주소 

##### TLB와 캐시의 차이점
TLB는 page table entry에 대한 caching 역할이고 캐시는 실질적인 메모리에 대한 캐싱은 캐시를 통해!

### TLB를 사용한 paging 동작 원리를 그리시오
바로 table에 접근하지 말고 TLB를 사용하여 hit이 아닌 경우 table로 접근
참조: OSOhYes 그림 8.3


### Segmentation과 paging의 차이를 말하고 각각의 장단점을 말하시오
| | Paging | Segmetation |
|--------|--------|-------------|
|공통점        | 둘다 virtual memory를 구현하는 방법임. 가상 주소와 실주소 매핑 과정과 원리가 같다      | (위의 mapping에 대한 질문은 모두 segmentation에 대해서도 동일하게 대답 가능)
|차이점1        | 프로그램 조각의 크기가 모두 같다        |프로그램 조각의 크기 모두 다르다 (논리적인 하나의 단위로)            |
|차이점2   | entry에 실주소 대신 frame number가 명시될 수도 있음        |entry에 실주소가 명시됨            |
|장점        | 구조가 단순하다        | sharing 시 유리, 코드 부분 수정 시 용이     |
|단점       |  내부 단편화      | 외부 단편화             |

cf ) 
Segmentation의 장점 자세한 설명:
논리적 단위로 나눔 -> 
1) sharing 시,간단하다(page의 사이즈가 다르다면 공유하고 싶은 위치에 대해서 맞추는 것이 복잡) 
2) 논리적 단위로 나누기 때문에 단위별로 수정하기 편하다         

### Segmented Paging에 대해서 설명해보시오

[필요성] segmentation의 논리적 단위로 나누어지는 것의 이점과 외부 단편화를 해결하기 위해  
[구조] segment가 page로 다시 나뉘어짐 (segment 마다 paging table)

원리 그림 이해하고 그려보기! (OSOhYes 그림 8.8 참조)



**Reference** 
[도표](https://www.geeksforgeeks.org/operating-system-paging/)

