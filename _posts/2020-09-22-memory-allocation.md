---
layout: post
title: "memory allocation은 무엇일까?"
date: 2020-09-22
author: 주진형
categories: study
tags: memory
---

안녕하세요 A.N.S.I 부원 주진형입니다.

오늘은 memory allocation에 대해서 알아보도록 하겠습니다.

<!--more-->

# 메모리 allocation

memory allocation이라는 것은 무엇일까요?

말 그대로 memory를 allocation 한다는 뜻입니다.

우리말로 번역하면 주 기억장치(ram)에서 메모리(memory는 메모리로 번역하겠습니다.)를 할당 한다는 뜻이죠.

주 기억장치에서 우리가 잠깐 동안 저장할 공간을 만든다고 보시면 되겠습니다.



## 주 기억장치? 보조 기억장치?

memory allocation에 대해서 이해하기 전에 잠깐동안 하드웨어의 명칭을 정리하고 가겠습니다.

### 주 기억장치

주 기억장치라고 함은 우리가 일반적으로 말하는 램을 말하는 것입니다. 컴퓨터 살 때 램 16기가, 램 32기가 이런 식으로 말하는 그 램을 말하는 것입니다.

주 기억장치는 휘발성 메모리로, **컴퓨터를 끄면 데이터가 전부 사라집니다**.

### 보조 기억장치

보조 기억장치는 우리가 일반적으로 말하는 ssd(플래시 메모리), hdd(하드 디스크)를 말합니다.
컴퓨터 살 때 말하는 ssd 512기가, 하드 2테라 이런 것들 말하는 것이죠.

보조 기억장치는 비 휘발성 메모리로 **컴퓨터를 꺼도 데이터가 사라지지 않습니다**.

### 그럼 무조건 보조 기억장치가 좋은 거 아닌가?
저만 고민 한 점일수도 있지만, 저는 왜 주 기억장치를 사용하는지 이해하지 못했습니다.
끄면 다 사라지는데 왜 쓰는거지? 전부 보조 기억장치로 사용하면 좋지 않나? 하는 의문점을 품었죠.

결론을 말씀드리면 보조 기억장치는 주 기억장치에 비해서 읽고 쓰는 속도가 굉장히 느립니다.

CrystalDiskMark 라는 읽고 쓰는 속도 벤치마크 프로그램으로 측정한 결과, ssd에 비해 ram이 3배 이상 빠른 것으로 보입니다. 실제로 체크를 해본다면 다른 결과가 나올 수 있음을 주의해주시길 바랍니다.
(출처: https://www.quora.com/Is-the-speed-of-SSD-and-RAM-the-same)

위의 결과처럼 일반적으로 보조 기억장치는 주 기억장치보다 읽고 쓰는 속도가 굉장히 느리기에 대부분 프로그램은 주 기억장치에 메모리를 할당하고 사용하게 됩니다.




## 다시 Memory allocation으로 돌아와서...

어디다가 메모리를 할당하는지는 확실히 알았을 거라고 생각합니다!

그럼 어떻게 메모리를 할당 할까요?

```c
#include<stdio.h>
int main(){
    int a = 0;
	return 0;
}
```
메모리를 할당 했습니다!

*malloc* 은 어디있냐고요?

### memory

메모리에도 종류가 있습니다. c, c++ 에서는 일반적으로 4개로 나누게 됩니다.

```stack```, ```data```, ```code```, ```heap```

![메모리 구조](http://tcpschool.com/lectures/img_c_memory_structure.png)

### code area

저희가 짠 코드가 들어가는 영역입니다. 코드 바이트수가 크면 클수록 이 영역의 크기가 커지게 됩니다.



### data area

```c
#include<stdio.h>
int globalVariable;
static int staticVariable;
int main(){
    return 0;
}
```

위 코드의 ```globalVariable``` 처럼 전역변수들이 들어가거나 ```staticVariable``` 이 들어가는 영역입니다.



### heap area

c 와 c++ 에서 **사용자가 직접 관리 해야하는 메모리 영역(매우매우 중요)**입니다.  최대 크기가 매우 크고 동적으로 메모리가 할당되고 해제되는 곳입니다.

c 와 c++ 가 unmanaged langauge 인 이유인 메모리 영역입니다.



### stack area

함수가 호출될 때 지역변수와 매개변수가 저장되는 영역입니다.

```c
#include<stdio.h>

int main(){
    int a = 0;
    int b = 2;
    return 0;
}
```

위의 코드에서 ```a, b``` 가 가지는 메모리 영역입니다.

**stack memory는 함수를 벗어날 때 stack 메모리는 해제되어서 사용자가 직접 메모리 관리를 해줄 필요가 없습니다.**



## 다시 다시 돌아와서...

다시 한번 코드를 봅시다.

```c
#include<stdio.h>
int main(){
    int a = 0;
	return 0;
}
```

이 코드는 stack area에 메모리가 할당 됩니다.

```malloc``` 만이 메모리 할당을 하는 것이 아니라는 것을 알려주기 위한 예제였습니다.



### 메모리 할당 방법

메모리를 할당 하는 방법은 여러가지가 있습니다.

#### 선언

```c
#include<stdio.h>
int main(){
    int a = 0;
    return 0;
}
```

위에서 설명드린 방법처럼 stack area에 메모리를 할당 할 수 있습니다.

하지만 이 방법은 일반적으로 메모리 할당이라고 말하지 않습니다. 왜냐하면 자동으로 메모리를 해제해주기 때문에 안전하기 때문이죠.

일반적으로 c 에서 말하는 메모리 할당은 아래의 방법들을 말합니다.



#### malloc

```c
#include<stdio.h>
#include<stdlib.h>
int main(){
    int* ptr = NULL;
    ptr = malloc(sizeof(int));
    free(ptr);
    return 0;
}
```

```void* malloc( size_t size );```  (출처: https://en.cppreference.com/w/c/memory/malloc)

```malloc``` 은  memory allocation의 줄임말로 ```stdlib.h``` 에 있는 메모리 할당 함수입니다. 

매개변수로 byte 단위 크기를 받고 heap area에 매개변수로 받는 size byte 만큼 크기를 할당합니다.



#### calloc

```c
#include<stdio.h>
#include<stdlib.h>
int main(){
    int* ptr = NULL;
    ptr = calloc(1, sizeof(int));
    free(ptr);
    return 0;
}
```

```void* calloc( size_t num, size_t size );``` (출처:https://en.cppreference.com/w/c/memory/calloc)

```calloc``` 은 clear allocation의 줄임말로, ```stdlib.h``` 에 있는 메모리 할당 함수 입니다.

```calloc``` 은 ```malloc``` 과 다르게 개수와, 사이즈를 받습니다. size 만큼을 num 개수만큼 할당하라는 뜻입니다.

```calloc``` 의 가장 큰 특징은 ```malloc``` 과 다르게 메모리 할당 후 쓰레기 값이 아닌 0으로 초기화 되어서 할당됩니다.



#### realloc

```c
#include<stdio.h>
#include<stdlib.h>
int main(){
    char* ptr = NULL;
    ptr = malloc(sizeof(char) * 10);
    ptr = realloc(ptr, sizeof(char) * 5);
    free(ptr);
    return 0;
}
```

```void *realloc(void *ptr, size_t size)``` (출처: https://en.cppreference.com/w/c/memory/realloc)

```realloc``` 은 reallocation 의 줄임말로 ```stdlib.h``` 에 있는 재 할당할 때 사용하는 함수입니다.

```NULL``` 이 return 되면 재할당에 실패했다는 뜻이고, 나머지는 할당에 성공하고, 그 포인터를 반환합니다.



이렇게 여러 방법으로 c에서 메모리 할당을 할 수 있습니다.



### 해제

c 와 c++이 unmanaged language 라는 이유는 heap 영역에 메모리가 할당되면, 사용자가 풀어주지 않으면 사용하지 않더라도 메모리를 할당한 채로 풀지 않기 때문입니다.



그래서 c 에서는 ```void free( void* ptr );``` (출처: https://en.cppreference.com/w/c/memory/free) 를 사용해 메모리를 해제 해주어야합니다.



#### 예제

```c
#include<stdio.h>
#include<stdlib.h>
int main(){
    char* ptr = NULL;
    ptr = malloc(sizeof(char) * 10);
    free(ptr);
    return 0;
}
```



# 결론

오늘은 memory의 구조와, memory allocation의 방법들, 주의점에 대해서 알아보았습니다.

### 요약

malloc은 힙메모리 할당, calloc은 malloc과 비슷하지만 값이 0으로 초기화. realloc은 새로 재할당.



**(중요!!)메모리 할당 후에는 반드시 free**



다음 post는 메모리 할당에 대한 조금 더 깊은 이해를 돕기 위한 post가 될 거 같습니다.



## 주석

포스트 중에서 틀린 점이나 궁금한 점이 있으면 ```카카오톡 주진형``` 으로 연락주시면 말씀해주세요.

이 주제가 궁금하다! 하면 똑같이 카카오톡으로 연락주시면 말씀해드리겠습니다.



감사합니다.

