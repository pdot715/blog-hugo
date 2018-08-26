---
title: "6E Linkedlist 문제 정리"
date: 2018-07-14T00:38:55+09:00
categories: [DS]
tags: [linkedlist, 6E]
---


https://github.com/careercup/CtCI-6th-Edition-cpp/blob/a68ba3e1c630a4d218ff1294f3eaf5aeced449ec/chapter-2-Linked-Lists/

github에 있는 코드의 주석을 번역함.

- 장점은 추가, 삭제 연산이 상수시간.
- 단점은 특정 인덱스 접근이 상수시간으로 안됨 (배열과 달리)

1번 정렬되지 않은 linked list에서 중복되는 원소 제거

- 접근방법 1 : iterating (공간복잡도 O(1), 시간복잡도 O(n^2))
- 접근방법 2 : hase table 사용 (공간복잡도 O(n), 시간복잡도 O(n))

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

2번 뒤에서 k번째 원소 구하기

단일 링크드리스트에서 뒤에서 k번째 원소를 구하는 문제 리스트의 길이가 주어지지 않는다고 가정 (길이를 알면 그냥 length-k번째 원소이니까)

## 접근 방법
### Iterative (시간복잡도 O(N), 공간복잡도 O(1))

- 포인터 2개 사용
- 첫번째 포인터를 k로 이동
- 두번째 포인터 (시작지점부터), 첫번째 포인터 (k+1 지점부터) 동시에 움직이게 함
- 두번째 포인터가 끝에 도달했다면 첫번째 포인터는 k번째 node에 있을 것

### 재귀적 방법 (공간복잡도 O(N))

- index가 현재 node를 추적하도록
- n-1 node에 대해 문제를 해결하고 index에 1 더하고
- 재귀적으로 호출된 부모 메서드는 그 값에 1을 더함
- counter가 k에 도달하면, 우리는 length - k node에 도달
- 이때 이 node가 뒤에서부터 k번째 node 이 소스코드의 방법은 C++의 참조를 통한 값 전달 기능 이용한 것. 포인터를 통해 노드를 반환할 수 있고, 카운터 값도 갱신 가능.

```cpp
#include <iostream>

struct Node {
  int data;
  Node * next;
  Node(int d) : data{ d }, next{ nullptr } { }
};


/**
 * Insert to the head of the list
 * @param head - Current head of list
 * @param data - new node's data
 */
void insert( Node * & head, int data ) {
  Node * newNode = new Node(data);
  newNode->next = head;
  head = newNode;
}

/**
 * [deleteList - delete the entire list]
 * @param head - head of the list
 */
void deleteList( Node * & head ) {
  Node * nextNode;
  while(head) {
    nextNode = head->next;
    delete(head);
    head = nextNode;
  }
}

/**
 * printList - Helper routine to print the list
 * @param head - Current head of the list.
 */
void printList( Node * head ) {
  while(head) {
    std::cout << head->data << "-->";
    head = head->next;
  }
  std::cout << "null" << std::endl;
}

/**
 * [kthToLastHelper - helper routine to find nth node for recursive approach
 * @param  head  - head of the list
 * @param  k     - the k value for finding kth element from last of the list.
 * @param  i     - an index maintained to keep track of current node.
 * @return       - kth node from last.
 */
Node * kthToLastHelper( Node * head, int k , int & i) {
  if ( head == nullptr ) {
    return nullptr;
  }

  Node * node = kthToLastHelper(head->next, k, i);
  i = i + 1;
  //if we have solved problem k times from last.
  if ( i == k ) {
    return head;
  }
  return node;
}

/**
 * kthToLastRecursive - Recursive approach for finding kth to last element of list.
 * @param  head  - head of node
 * @param  k     - the k value for finding kth element from last of the list.
 * @return       - kth node from last.
 */
Node * kthToLastRecursive( Node * head, int k ) {
  int i = 0;
  return kthToLastHelper(head, k, i);
}

/**
 * kthToLastIterative -  Iterative approach for finding kth to last element of list.
 * @param  head  - head of node
 * @param  k     - the k value for finding kth element from last of the list.
 * @return       - kth node from last.
 */
Node * kthToLastIterative( Node * head, int k ) {
  if ( head == nullptr ) {
    return head;
  }

  Node * ptr1 = head;
  Node * ptr2 = head;

  int i = 0;
  while( i < k && ptr1 ) {
    ptr1 = ptr1->next;
    ++i;
  }

  //out of bounds
  if ( i < k ) {
    return nullptr;
  }

  while( ptr1 != nullptr ) {
    ptr1 = ptr1->next;
    ptr2 = ptr2->next;
  }

  return ptr2;
}



int main() {
  Node * head = nullptr;
  for ( int i = 7; i > 0; i-- ) {
    insert(head, i);
  }
  std::cout << "List: ";
  printList(head);

  std::cout << "4th node from last (Recursive) : ";
  Node *node4 = kthToLastRecursive(head, 4);
  if ( node4 != nullptr ) {
    std::cout << node4->data << std::endl;
  } else {
    std::cout << "NULL NODE\n";
  }

  std::cout << "4th node from last (Iterative) : ";
  node4 = kthToLastIterative(head, 4);
  if ( node4 != nullptr ) {
    std::cout << node4->data << std::endl;
  } else {
    std::cout << "NULL NODE\n";
  }

  deleteList(head);

  return 0;
}
```

3번 중간 노드 삭제
- 접근 방법 :  A 노드를 리스트에서 제거하기 위해서는 A의 이전 노드를 A의 다음 노드와 연결시켜야한다. 하지만 우리는 A의 이전 노드에 접근할 방법이 없다. 해법은 다음 노드의 데이터를 현재 노드에 복사한 다음, 다음 노드를 지우는 것이다

```cpp
#include <iostream>

            using namespace std;

            struct Node {
                char data;
                Node* next;
                Node(char c) : data{ c }, next{ nullptr } {}
            };

            void printList(Node* head) {
                while (head) {
                    cout << head->data << "-->";
                    head = head->next;
                }
                cout << "nullptr" << endl;
            }

            // @param node (삭제되어야 할 node)
            void deleteNode(Node* node){
                if (node == nullptr || node->next == nullptr) {
                    return;
                }

                Node* nextNode = node->next;
                node->data = nextNode->data;
                node->next = nextNode->next;
                delete nextNode;
            }

            int main() {
                Node* head = new Node('a');
                head->next = new Node('b');
                head->next->next = new Node('c');
                head->next->next->next = new Node('d');
                head->next->next->next->next = new Node('e');

                cout << "List before deleteion:\n";
                printList(head);
                cout << "deleting node containing data as 'c'\n";
                deleteNode(head->next->next);
                cout << "List after deletion : \n";
                printList(head);
                return 0;

            }
```

- 이 방법은 삭제할 노드가 리스트의 마지막 노드면 풀 수 없다.

4번 분할

- 접근 방법 : 첫 노드부터 시작, x보다 큰 건 tail 부분에 x보다 작은건 head부분에 추가. 
- 입력으로 주어진 연결리스트를 순회하면서 tail list나 head list에 원소를 추가함.
- 연결리스트의 마지막에 도달하면 분할이 끝났으므로 두 리스트를 합치면 됨.

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

5번 리스트의 합

접근방법
- recursive
- iteraitve

```cpp



#include <iostream>

using namespace std;

struct Node {
	int data;
	Node* next;
	Node(int d) : data{ d }, next{ nullptr } {}
};

void insert(Node* &head, int data) {
	Node* newNode = new Node(data);
	newNode->next = head;
	head = newNode;
}

void printList(Node* head) {
	while (head) {
		cout << head->data << "-->";
		head = head->next;
	}
	cout << "nullptr" << endl;
}

Node* add_iterative(Node* list1, Node* list2) {
	if (list1 == nullptr) {
		return list2;
	}

	if (list2 == nullptr) {
		return list1;
	}

	// list3은 결과값을 저장하기 위해서
	Node* list3 = nullptr;
	// list3의 꼬리 부문에 새로운 Node 추가 위해서
	Node* list3Tail = nullptr;

	int value = 0, carry = 0;

	while (list1 || list2) {

		// value를 더한다. 만약 이미 탐색한 것이라면 0을 더한다.
		value = carry + (list1 ? list1->data : 0) + (list2 ? list2->data : 0);

		// 새로운 value와 carry를 구한다.
		if (value > 9) {
			carry = 1;
			value = value % 10;
		} else {
			carry = 0;
		}

		// 새로운 node
		Node* temp = new Node(value);

		// 만약 첫 노드면 
		if (list3 == nullptr) {
			list3 = temp;
		}
		else {
			list3Tail->next = temp;
		}

		// 새로운 tail 만들기
		list3Tail = temp;

		if (list1) {
			list1 = list1->next;
		}
		if (list2) {
			list2 = list2->next;
		}

		if (carry > 0) {
			list3Tail->next = new Node(carry);
		}
		return list3;
	}
}


Node* add_recursive(Node* list1, Node* list2, int carry) {
	if (list1 == nullptr && list2 == nullptr && carry == 0) {
		return nullptr;
	}

	int value = carry;
	if (list1) {
		value += list1->data;
	}
	if (list2) {
		value += list2->data;
	}

	Node* resultNode = new Node(value % 10);
	resultNode->next = add_recursive(list1 ? (list1->next) : nullptr,
									 list2 ? (list2->next) : nullptr,
									 value > 9 ? 1 : 0);
	return resultNode;
}


void padList(Node* &list, int padding) {
	for (int i = 0; i < padding; ++i) {
		insert(list, 0);
	}
}

int length(Node* head) {
	int len = 0;
	while (head) {
		len++;
		head = head->next;
	}
	return len;
}

Node* add_followup_helper(Node* list1, Node* list2, int &carry) {
	if (list1 == nullptr && list2 == nullptr && carry == 0) {
		return nullptr;
	}

	Node* result = add_followup_helper(list1 ? (list1->next) : nullptr,
		list2 ? (list2->next) : nullptr, carry);
	int value = carry + (list1 ? list1->data : 0) + (list2 ? list2->data : 0);
	insert(result, value % 10);
	carry = (value > 9) ? 1 : 0;
	return result;
}

Node* add_followup(Node* list1, Node* list2) {
	int len1 = length(list1);
	int len2 = length(list2);

	// pad the smaller list
	if (len1 > len2) {
		padList(list2, len1 - len2);
	}
	else {
		padList(list1, len2 - len1);
	}
	int carry = 0;
	Node* list3 = add_followup_helper(list1, list2, carry);
	if (carry)  {
		insert(list3, carry);
	}
	return list3;
}


void deleteList(Node* &head) {
	Node* nextNode;
	while (head) {
		nextNode = head->next;
		delete(head);
		head = nextNode;
	}
}


int main() {
	// list1은 617로
	Node* list1 = nullptr;
	insert(list1, 6);
	insert(list1, 1);
	insert(list1, 7);
	cout << "List 1 : ";
	printList(list1);

	// list2은 295로
	Node* list2 = nullptr;
	insert(list2, 2);
	insert(list2, 9);
	insert(list2, 5);
	cout << "List 2 : ";
	printList(list2);

	Node* list3 = add_iterative(list1, list2);
	cout << "Iterative soulution: \n";
	cout << "List 3 : ";
	printList(list3);

	Node* list4 = add_recursive(list1, list2, 0);
	cout << "recursive solution : \n";
	cout << "List 4 : ";
	printList(list4);

	deleteList(list1);
	deleteList(list2);
	deleteList(list3);
	deleteList(list4);


	cout << "\n\nNow follow up casae, list are stroed such that 1's digit is at the tail of list\n";
	// Node* listx = nullptr;
	insert(list1, 4);
	insert(list1, 3);
	insert(list1, 2);
	insert(list1, 9);
	cout << "List1 : ";
	printList(list1);

	insert(list2, 9);
	insert(list2, 9);
	insert(list2, 8);
	cout << "List2 : ";
	printList(list2);

	list3 = add_followup(list1, list2);
	cout << "adding two above lists\n";
	cout << "List 3: ";
	printList(list3);

	deleteList(list1);
	deleteList(list2);
	deleteList(list3);

	return 0;
}

```

이거는 나중에 다시 해보기,,, 



6번 회문 검사

- 접근방법 1 : 리스트의 절반을 뒤집고 나머지 절반과 비교
- 접근방법 2 : Iterative
    - 리스트의 절반을 스택에 push
    - 스탯에서 pop 하면서 비교한다
- 접근방법 3 : Recursive
```cpp
#include <iostream>
#include <stack>

using namespace std;

struct Node {
	char data;
	Node* next;
	Node(char c) : data{ c }, next{ nullptr } {}
};

void insert(Node* &head, char c) {
	Node* newNode = new Node(c);
	newNode->next = head;
	head = newNode;
}

void printList(Node* head) {
	while (head) {
		cout << head->data << "-->";
		head = head->next;
	}
	cout << "nullptr" << endl;
}

void reverse(Node* &head) {
	if (head == nullptr || (head && (head->next == nullptr))) {
		return;
	}
	Node* newHead = nullptr;
	Node* nextNode = nullptr;
	while (head) {
		nextNode = head->next;
		head->next = newHead;
		newHead = head;
		head = nextNode;
	}
	head = newHead;
}

bool isPalindromeIter1(Node* head) {

	// 리스트가 비어있거나 하나의 노드만 있는 경우
	if (head == nullptr || head->next == nullptr) {
		return true;
	}

	Node* ptr1 = head;
	Node* ptr2 = head;
	Node* middleNode = nullptr;

	while (ptr2 && ptr1 && ptr1->next) {
		ptr1 = ptr1->next->next;
		ptr2 = ptr2->next;
	}

	// node 개수가 홀수가면 중간에 있는거 하나 건너뜀
	if (ptr1 && ptr1->next == nullptr) {
		ptr2 = ptr2->next;
	}

	// 리스트의 뒷 절반 뒤집기
	reverse(ptr2);

	middleNode = ptr2;
	// 이제 리스트 절반으로 나눈거 두 개 비교
	ptr1 = head;

	while (ptr1 && ptr2 && ptr1->data == ptr2->data) {
		ptr1 = ptr1->next;
		ptr2 = ptr2->next;
	}

	reverse(middleNode);

	if (ptr2 == nullptr) {
		return true;
	}
	else {
		return false;
	}
}


bool isPalindromeIter2(Node* head) {
	// 리스트가 비어있거나 하나만 있거나 한 경우
	if (head == nullptr || head->next == nullptr) {
		return true;
	}

	Node* ptr1 = head;
	Node* ptr2 = head;

	// 리스트 앞 절반부분에 스택 푸쉬
	stack<Node*> nodeStack;

	while (ptr2 && ptr2 && ptr1->next) {
		ptr1 = ptr1->next->next;
		nodeStack.push(ptr2);
		ptr2 = ptr2->next;
	}

	// node 개수가 홀수면 중간 하나 생략
	if (ptr1 && ptr1->next == nullptr) {
		ptr2 = ptr2->next;
	}

	// 이제 스택에 넣었던것과 나머지 절반과 비교
	while (!nodeStack.empty() && ptr2) {
		Node* curr = nodeStack.top();
		nodeStack.pop();
		if (curr->data != ptr2->data) {
			return false;
		}
		ptr2 = ptr2->next;
	}

	return true;
}

bool isPalindromeRecurHelper(Node* &left, Node* right) {

	// right가 nullptr이 되면 그만그만
	if (right == nullptr) {
		return true;
	}

	// 리스트의 나머지가 회문이 되게
	bool isPalindrome = isPalindromeRecurHelper(left, right->next);
	if (!isPalindrome) {
		return false;
	}

	// 현재 node의 value를 체크
	isPalindrome = (left->data == right->data);

	// 다음 call을 위해서 left를 left->next로
	left = left->next;

	return isPalindrome;
}

bool isPalindromeRecur(Node* head) {
	return isPalindromeRecurHelper(head, head);
}

int main() {
	Node* head1 = nullptr;
	insert(head1, 'a');
	insert(head1, 'b');
	insert(head1, 'c');
	insert(head1, 'c');
	insert(head1, 'b');
	insert(head1, 'a');
	cout << "List 1 : ";
	printList(head1);
	if (isPalindromeIter1(head1)) {
		cout << "List 1 is pallindrome list\n";
	}
	else {
		cout << "List 1 is not a pallindrome list\n";
	}
	cout << "list 1 : ";
	printList(head1);

	Node* head2 = nullptr;
	insert(head2, 'r');
	insert(head2, 'a');
	insert(head2, 'd');
	insert(head2, 'a');
	insert(head2, 'r');
	cout << "List 2 : ";
	printList(head2);

	if (isPalindromeIter2(head2)) {
		cout << "List 2 is pallindrome list\n";
	}
	else {
		cout << "List 2 is not a pallindrome list \n";
	}

	cout << "List 2 : ";
	printList(head2);

	Node* head = nullptr;
	insert(head, 'a');
	insert(head, 'b');
	insert(head, 'c');
	insert(head, 'b');
	insert(head, 'd');


	if (isPalindromeRecur(head)) {
		cout << "List 3 is pallindrome list \n";
	}
	else {
		cout << "list 3 is not a pallindrome list \n";
	}

	cout << "list 3 : ";
	printList(head);
	return 0;

}


```

7번 교집합 (노드의 주소가 완전히 같은 경우)

```cpp
/*
2개의 링크드리스트가 주어지고 같은 지점을 교차할때
그 교차포인트를 리턴함 (없으면 nullptr)
교차 판단은 reference (value 아님)
*/ 

#include <iostream>
#include <cmath>

using namespace std;

struct Node {
	int data;
	Node* next;
	Node(int d) : data {d}, next{nullptr} {}
};

void printList(Node* head) {
	while(head) {
		cout << head->data << "-->";
		head= head->next;
	}
	cout << "NULL" << endl;
}

int listLen(Node* head) {
	int count = 0;
	while(head) {
		head = head->next;
		count++;
	}
	return count;
}

Node* intersectionPoint(Node* head1, Node* head2) {
	int len1 = listLen(head1);
	int len2 = listLen(head2);

	Node* ptr1 = (len1 > len2) ? head1 : head2;
	Node* ptr2 = (len1 > len2) ? head2 : head1;

	int i = 0;
	while (i < abs(len1-len2) && ptr1) {
		ptr1 = ptr1->next;
	}

	while(ptr1 && ptr2) {
		if(ptr1 == ptr2) {
			return ptr1;
		}
		ptr1=ptr1->next;
		ptr2=ptr2->next;
	}

	return nullptr;
}


int main() {
	Node* list1 = new Node(3);
	list1->next = new Node(6);
	list1->next->next = new Node(9);
	list1->next->next->next  = new Node(12);
	list1->next->next->next->next = new Node(15);
	list1->next->next->next->next->next  = new Node(18);

	Node* list2= new Node(7);
	list2->next = new Node(10);
	list2->next->next = list1->next->next->next;

	printList(list1);
	printList(list2);

	Node* intersectingNode = intersectionPoint(list1, list2);
	if(intersectingNode) {
		cout << " intersecting Node of lists is : " << intersectingNode->data << endl;
	} else {
		cout << "Lists do not interset" << endl;
	}
	return 0;
}

```
8번 루프 발견
- 조건 : 순환 연결리스트가 주어짐 (node의 next 포인터가 앞선 노드들 가운데 어느 하나를 가리키도록 되어있는 연결리스트)
- 구해야하는 것 : 순환되는 부분의 첫째 노드
- 해결 방법
	- 1단계 : 연결리스트에 루프가 있는지 검사
		- FastRunner (한 번에 두 걸음), SlowRunner (한 번에 한 걸음) 접근법 사용


```cpp
#include <iostream>


using namespace std;

struct Node {
	int data;
	Node* next;
	Node(int d) : data{ d }, next{ nullptr } {}
};

void removeLoop(Node* loopNode, Node* head) {
	Node* ptr1 = head;
	Node* ptr2 = loopNode;
	while (ptr1->next != ptr2->next) {
		ptr1 = ptr1->next;
		ptr2 = ptr2->next;
	}

	ptr2->next = nullptr;
}

bool detectAndRemoveCycle(Node* head) {
	if (head == nullptr) {
		return false;
	}

	Node* fastPtr = head;
	Node* slowPtr = head;

	while (slowPtr && fastPtr && fastPtr->next) {
		fastPtr = fastPtr->next->next;
		slowPtr = slowPtr->next;
		if (fastPtr == slowPtr) {
			removeLoop(slowPtr, head);
			return true;
		}
		
	}
	return false;
}


void insert(Node* &head, int data) {
	Node* newNode = new Node(data);
	if (head == nullptr) {
		head = newNode;
	}
	else {
		Node* temp = head;
		while (temp->next != nullptr) {
			temp = temp->next;
		}
		temp->next = newNode;
	}
}


void printList(Node* head) {
	while (head) {
		cout << head->data << "-->";
		head = head->next;
	}
	cout << "NULL" << endl;
}

int main() {
	Node* head = nullptr;
	insert(head, 1);
	insert(head, 2);
	insert(head, 3);
	insert(head, 4);
	insert(head, 5);
	std::cout << "Current List:\n";
	printList(head);
	cout << "Inserting loop, connecting 5 to 2\n";
	head->next->next->next->next->next = head->next;
	std::cout << "Detecting and deleting loop\n";
	detectAndRemoveCycle(head);
	std::cout << "Back to the same old list\n";
	printList(head);
	return 0;
}

```
