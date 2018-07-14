---
title: "단일 링크드리스트 정리했던 것들"
date: 2018-07-14T18:06:55+09:00
categories: [ds]
tags: [linkedlist,leetcode,hackerearth]
---

여러 사이트에서 검색해보면서 정리했던 것들 모음
[https://leetcode.com/explore/learn/card/linked-list/209/singly-linked-list/1287/](https://leetcode.com/explore/learn/card/linked-list/209/singly-linked-list/1287/)

Singly-linked list의 각각의 node는 value뿐만 아니라 다음 node로 가는 link인 reference field를 포함하고 있다. 이러한 방법으로 Singly-linked list는 모든 node들이 순서대로 조직된다.

```c
    // Definition for singly-linked list.
    struct SinglyListNode {
        int val;
        SinglyListNode *next;
        SinglyListNode(int x) : val(x), next(NULL) {}
    };
```

대부분의 경우에 우리는 head node(first node)를 이용해 전체 리스트를 대표한다.

## 연산

배열과 달리 우리는 singly-linked list에서 무작위 element를 상수 시간(constant time)으로 접근하는게 불가능하다. 우리가 i번째 element를 접근할 때 우리는 head node부터 순서대로 순회해야한다. index를 이용하여 element에 접근하는 시간은 평균적으로 O(N)이 걸린다. 여기서 N은 linked list의 길이다.

배열과 비교해서, index를 통해 data를 접근할 때의 나쁜 퍼포먼스에도 불구하고 왜 linked list를 사용하는지 궁금할 것이다. 우리는 insert와 delete 연산을 다음 포스트를 통해 소개할 것이고 이를 통해 당신은 linked list 사용의 유익한 점을 알 수 있을 것이다.

[https://leetcode.com/explore/learn/card/linked-list/209/singly-linked-list/1288/](https://leetcode.com/explore/learn/card/linked-list/209/singly-linked-list/1288/)

우리가 새로운 value를 prev라는 node 다음에 추가하기를 원할때 이렇게 할 수 있다.

1.  cur이라는 새로운 node를 선언한다. 
2.  cur의 "next" 필드에 prev의 다음 node인 next를 가리키게 한다. 
3.  prev의 "next" 필드에 cur를 가리키게 한다.

배열과 다르게, 우리는 삽입하려는 지점의 모든 element를 이동할 필요가 없다. 그래서 우리는 새로운 node를 삽입할 때 O(1) 시간만에 넣을 수 있고 매우 효과적이다.

## 새로운 node를 맨 처음 지점에 추가하는 방법

우리가 아는 것처럼, head node는 전체 리스트를 대표하는데 사용한다. 그러므로 새로운 node를 맨 앞에 넣을때는 head에 대해서 업데이트 해주는게 중요하다.

1.  cur이라는 새로운 node를 선언한다
2.  새로운 node가 우리의 원래 head node였던 head를 가리키게 한다.
3.  cur를 head에 assign한다.

<hr>

우리가 singly linked list에 존재하는 cur이라는 node를 제거하기 원한다면, 이렇게 하면 된다.

1.  cur의 이전 node인 prev와 다음 node인 next를 찾는다.
2.  prev가 next를 가리키게 한다.

첫단계로 우리는 prev와 next를 찾아야한다. next를 찾는것은 쉽다. cur의 레퍼런스 필드를 참고하면 된다. 하지만 우리가 prev를 찾는데에는 head node부터 순서대로 탐험해야하기 때문에 평균적으로 O(N)이라는 시간이 걸린다. 여기서 N은 linked list의 길이다. 그렇기때문에 node를 제거하는데 걸리는 시간 복잡도는 O(N)이다.

공간복잡도는 O(1)이다. 왜냐면 우리는 우리의 포인터를 저장하기 위한 constant space만 필요하기 때문이다.

1.  head부터 prev가 나올때까지 순회하여 prev를 찾는다. 
2.  prev와 next를 연결한다.

## 첫번째 Node 삭제

우리가 첫번째 Node를 지우기 원할때는 전략이 약간 다르다. 우리가 첫번째 Node를 지울때는, 간단하게 원래 head node의 다음 node를 head로 assign하면 된다. 

링크드리스트는 head node부터 시작하기때문에 node 23은 더이상 우리의 linked list에 존재하지 않는다.

<hr>
[https://www.hackerearth.com/practice/data-structures/linked-list/singly-linked-list/tutorial/](https://www.hackerearth.com/practice/data-structures/linked-list/singly-linked-list/tutorial/)

링크드리스트는 element의 집합을 저장하는 방법이다. 링크드리스트에서 각각의 element는 node의 형태로 저장된다.

**Node:**

Node는 2개의 sub-element의 집합이다. Data는 element를 저장하고 Next는 다음 Node로 향하는 링크를 저장한다.

**Linked List:**


링크드리스트는 node들이 chain 형태로 이어져있는 구조다. 각각의 node는 순서대로 다음 노드를 가리킨다. 처음에 있는 node는 언제나 리스트 순회를 위한 레퍼런스로 사용되며 이를 HEAD라 부른다. 마지막 node는 NULL을 가리킨다.

**링크드리스트 선언:**

C언어에서 링크드리스트는 구조체와 포인터로 구현할 수 있다.
```c
    struct LinkedList{
      int data;
      struct LinkedList *next;
    };
```

위의 정의는 리스트에서 node를 만들때마다 사용된다. data는 element를 저장하고 next는 다음 주소를 향하는 주소가 있는 포인터를 저장한다. 근데 next에서 뭔가 이상한 점이 느껴지지 않는가? data type에 strcut LinkedList가 쓰여있다. 그 이유는 next가 self-referencing pointer이기 때문이다. sele-referencing pointer는 자신의 일부인 것을 가리키는 pointer다. 여기서 next는 node의 일부이면서 다음 node를 가리킨다.

**node 만들기**

```c
    typedef struct LinkedList *node // node를 struct LinkedList 데이터 타입의 포인터로 정의

    node createNode() {
      node temp; // node 선언
      temp = (node) malloc(sizeof(struct LinkedList)); // malloc()를 사용하여 메모리 할당
      temp->next = NULL; // 다음 지점을 NULL로
      return temp; // 새로 만들어진 node 리턴
    }
```

**링크드리스트에 node 추가:**

```c
    node addNode(node head, int value) {
      node temp, p; // temp, p 두 node 선언
      temp = createNode(); //  createNode는 새로운 node를 리턴 (next가 NULL을 가리키는)
      temp->data = value; // node의 data value를 추가
      if (head == NULL) {
        head = temp; // 링크드리스트가 empty일때
      } 
      else {
        p = head; // p를 head로
        while(p->next != NULL) {
          p = p->next; // p가 마지막 node가 될때까지 리스트 순회. 마지막 node는 언제나 NULL 가리킴
        }
          p->next = temp; // 이전 마지막 node가 새로운 node를 가리키게
      }
      return head;
    }
```

이 방법으로 언제나 새로운 node는 마지막 node 다음에 추가된다.

**리스트 순회**

head node를 시작 지점으로 사용하는 while 반복문을 통해서 링크드리스트를 순회할 수 있다

```c
    node p;
    p = head;
    while(p != NULL) {
      p = p->next;
    }
```

