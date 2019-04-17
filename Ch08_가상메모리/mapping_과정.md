# Page Table은 어디에 저장되고 어제 만들어지는가? (mapping table 생성 및 활용 과정) 
-> segmentation도 과정은 거의 같으므로 두 케이스에 모두에 해당되는 답이 됨
segmentation vs page 개념에 대해서는 다음 참조(summary.md의 Line6~10 )


## 기존 프로그램 메모리 적재 및 실행 과정
1) 하드 디스크에 저장되어 있던 프로그램 정보가 메모리에 모두 올라감
2) 메모리에 올라가는 형태
Neumann 구조에서 isntruction과 data가 실질적으로 같은 memory에 저장되지만 영역을 나누어 segment로 구조화 한다.
> 
.text segment : machine instruction
.data segment : static data
.stack segment : temporary storage, LIFO, 함수 값
.heap segment : dynamically allocated memory
3) instruction을 읽으면서 memory를 사용하여 프로그램 실행된다

## paging 또는 segmentation이 사용시 프로그램이 메모리에 적재되고 실행되는 과정
1) process를 시작하면 OS가 program이 조각내어 (page로 또는 segmentation으로) table을 생성하여 OS의 virtual memory(hardwarewise, DRAM)에 저장됨
- 단순하게는 메모리에 저장된다고 생각해도 됨. 하지만 실질적으로는 table의 크기가 크기 때문에 multi level table구조로 만들어 table도 부분적으로만 메모리에 적재하기도 한다

***Reference***
[Where in the computer architecture is the page table stored in?](https://www.quora.com/Where-in-the-computer-architecture-is-the-page-table-stored-in)
[where are the page tables stored?](https://www.reddit.com/r/compsci/comments/3c1b0z/where_are_the_page_tables_stored/)
[where is page table stored in linux?](https://unix.stackexchange.com/questions/487052/where-is-page-table-stored-in-linux)
2) instruction에서 사용되는 메모리는 모두 table을 거쳐서 실제 addr.로 변환되어 참조된다

