---
title: "6E Stack, Queue 문제 정리"
date: 2018-07-14T00:38:55+09:00
categories: [DS]
tags: [stack, queue, 6E]
---
https://github.com/careercup/CtCI-6th-Edition-cpp/tree/a68ba3e1c630a4d218ff1294f3eaf5aeced449ec/Chapter-3-Stacks-and-Queues

# 스택 구현 (stack.hpp)

```cpp
#include <utility>

using namespace std;

template <typename T>
class Stack {
    public:
        Stack() : top(nullptr), stackSize(0) {}
        Stack(Stack &&other) : top(move(other.top)), stackSize(move(other.stackSize)) {}
        ~Stack() { while(!isEmpty()) { pop(); } }

        template <typename U>
        void push(U &&value) {
            auto n = new Node(forward<U>(value), top);
            top = n;
        }

        T& peek() {
            if(!top) { 
                throw StackIsEmptyException();
            }
            return top->value;
        }

        T pop() {
            if(!top) {
                throw StackIsEmptyException();
            }
            auto value(move(top->value));
            auto n = top;
            top = n->next;
            delete n;
            --stackSize;
            return value;
        }

        bool isEmpty() const {
            return !top;
        }

        size_t size() const {
            return stackSize;
        }

        class StackIsEmptyException { };

    private:
        struct Node {
            Node(T& &v, Node* n) : value(move(v)), next(n) {}
            Node(const T &v, Node *n) : value(v), next(n) {}

            T value;
            Node* next; 
        };

        Node *top;
        size_t stackSize;
};

```

# 큐 구현 (queue.hpp)

```cpp
#include <utility>

template <typename T>
class Queue {
    public:
        Queue() : first(nullptr), last(nullptr), queueSize(0) { }
        Queue(Queue &&other) : first(move(other.first)), last(move(other.last)), queueSize(move(other.queueSize))
        ~Queue() { while(!isEmpty()) { remove(); }}


    template <typename U>
    void add(U &&value) {
        auto n = new Node(forward<U>(value));
        if(!first) { 
            first = n; 
        } else { 
            last->next = n;
        }
        last = n;
        ++queueSize;
    }

    T &peek() {
        if(!first) { 
            throw QueueIsEmptyException(); 
        } 
        return first->value;
    }

    T remove() {
        if(!first) {
            throw QueueIsEmptyException();
        }
        auto value(move(first->value));
        auto n = first;
        first = n->next;
        if(!first) { 
            last = nullptr;
        }
        delete n;
        --queueSize;
        return value;
    }

    bool isEmpty() const {
        return !first;
    }

    size_t size() const {
        return queueSize;
    }

    class QueueIsEmptyExcetpion {};
private:
    struct Node {
        Node(T &&v): value(move(v)), next(nullptr) {}

        T value;
        Node* next;
    };
    
    Node* first;
    Node* last;
    size_t queueSize;

};
```

# 1번 한 개로 세개 : 배열 한 개로 스택 세 개 구현

## 접근법 1 : 고정크기 할당

## 접근법 2 : 
