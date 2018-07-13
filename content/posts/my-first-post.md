---
title: "My First Post"
date: 2018-07-14T00:38:55+09:00
categories: [hugo]
tags: [static site generator]
---

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