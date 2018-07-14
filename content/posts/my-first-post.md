---
title: "6E Linkedlist 문제 정리"
date: 2018-07-14T00:38:55+09:00
categories: [DS]
tags: [linkedlist, 6E]
---


https://github.com/careercup/CtCI-6th-Edition-cpp/blob/a68ba3e1c630a4d218ff1294f3eaf5aeced449ec/chapter-2-Linked-Lists/

github에 있는 코드의 주석을 번역함.

연결리스트의 장점은 추가, 삭제 연산이 상수시간.
단점은 특정 인덱스 접근이 상수시간으로 안됨 (배열과 달리)

1. 정렬되지 않은 linked list에서 중복되는 원소 제거

접근방법 1 : iterating (공간복잡도 O(1), 시간복잡도 O(n^2))
접근방법 2 : hase table 사용 (공간복잡도 O(n), 시간복잡도 O(n))

```cpp
  #include <iostream>
            #include <unordered_map>
            #include <random>

            using namespace std;

            struct Node {
                int data = 0;
                Node* next = nullptr;
            };

            // insert : node를 list의 head에 삽입
            // head : list의 head
            // data : 새로운 node의 data

            void insert(Node* &head, int data) {
                Node* newNode = new Node;
                newNode->data = data;
                newNode->next = head;
                head = newNode;
            }

            // printList : 리스트 출력용 
            void printList(Node* head) {
                while (head) {
                    cout << head->data << "-->";
                    head = head->next;
                }
                cout << "nullptr" << endl;
            }

            // min값과 max값 사이에서 랜덤한 int값 생성
            // random한 값을 생성해서 테스트 용도로 사용...
            static inline int random_range(const int min, const int max) {
                random_device rd;
                mt19937 mt(rd());
                uniform_int_distribution<int> distribution(min, max);
                return distribution(mt);
            }

            /* 
            해법 1, 공간복잡도 O(1), 시간복잡도 O(n^2)
            removeDuplicates : 임시 버퍼 사용 없이 중복 제거
            여기서 Runner (부가 포인터) 기법 사용.
            연결리스트를 순회할 때 2개의 포인터를 동시에 사용. (이때 한 포인터가 다른 포인터보다 앞서도록)
            여기서는 curr 포인터는 연결리스트를 순회하고, runner 포인터는 그 뒤에 중복되는 원소가 있는지 확인
             */

            void removeDuplicates(Node* head) {
                if (head == nullptr || (head && (head->next == nullptr))) {
                    return;
                }
                Node* curr = head;
                while (curr) {
                    Node* runner = curr;
                    while (runner->next != nullptr) {
                        if (runner->next->data == curr->data) {
                            // 단방향 연결리스트에서 노드 삭제는 prev.next를 n.next와 같도록 하면 됨!
                            // 유의할 점은 널포인터 검사를 반드시 해야함. 
                            runner->next = runner->next->next;
                        }
                        else {
                            runner = runner->next;
                        }
                    }
                    curr = curr->next;
                }
            }

            // 해법 2, 공간복잡도 O(n), 시간복잡도 O(n)
            // hash table 사용

            void removeDuplicates1(Node* head) {
                if (head == nullptr || (head && (head->next == nullptr))) {
                    return;
                }
                unordered_map<int, int> node_map;
                Node* prev = head;
                Node* curr = head->next;
                node_map[head->data] = 1;

                while (curr != nullptr) {
                    while (curr && node_map.find(curr->data) != node_map.end()) {
                        curr = curr->next;
                    }
                    prev->next = curr;
                    prev = curr;
                    if (curr) {
                        node_map[curr->data] = 1;
                        curr = curr->next;
                    }
                }
            }

            int main() {
                cout << "method 1 : \n";
                Node* head = nullptr;
                for (int i = 0; i < 10; ++i) {
                    insert(head, random_range(1, 7));
                }
                printList(head);
                removeDuplicates(head);
                printList(head);

                cout << "method 2: \n";
                Node* head1 = nullptr;
                for (int i = 0; i < 10; ++i) {
                    insert(head1, random_range(1, 7));
                }
                printList(head1);
                removeDuplicates(head1);
                printList(head1);
                return 0;
            }
```


접근 방법 : 첫 노드부터 시작, x보다 큰 건 tail 부분에 x보다 작은건 head부분에 추가. 

입력으로 주어진 연결리스트를 순회하면서 tail list나 head list에 원소를 추가함. 

연결리스트의 마지막에 도달하면 분할이 끝났으므로 두 리스트를 합치면 됨.

```cpp
#include <iostream>
#include <random>

using namespace std;

// Node 구조체 선언
struct Node {
	int data;
	Node *next;
	Node(int d) : data{ d }, next{ nullptr } {}
};

// Node 삽입
void insert(Node* &head, int data) {
	Node* newNode = new Node(data);
	if (head == nullptr) { // 리스트가 비어있다면 새로운 node를 head로
		head = newNode;
	}
	else {
		Node* curr = head;
		while (curr->next) {
			curr = curr->next;
		}
		curr->next = newNode; // 리스트의 맨 뒤에 추가
	}
}

void printList(Node* head) {
	while (head) {
		cout << head->data << "-->";
		head = head->next;
	}
	cout << "nullptr" << endl;
}

// pivot보다 큰 element는 tail list에, 작은 elements는 head list에 추가
Node* partition(Node* listhead, int x) {
	Node* head = nullptr;
	Node* headInitial = nullptr; // head list의 시작 node
	Node* tail = nullptr;
	Node* tailInitial = nullptr; // tail list의 시작 node
	Node* curr = listhead;
	while (curr != nullptr) {
		Node* nextNode = curr->next;
		if (curr->data < x) {
			if (head == nullptr) {
				head = curr;
				headInitial = head;
			}
			// head list에 추가
			head->next = curr;
			head = curr; 
		}
		else {
			if (tail == nullptr) {
				tail = curr;
				tailInitial = tail;
			}
			// tail list에 추가
			tail->next = curr;
			tail = curr;
		}
		curr = nextNode;
	}

	head->next = tailInitial; // head list와 tail list 연결!
	tail->next = nullptr;
	return headInitial;
}


int main() {
	Node* head = nullptr;
	for (int i = 0; i < 10; ++i){
		insert(head, rand() % 9);
	}
	cout << "list before partition around 5 : \n";
	printList(head);
	cout << "list after partition around 5: \n";
	printList(partition(head, 5));
	return 0;
}
```