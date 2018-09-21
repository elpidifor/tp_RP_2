# tp_RP_2

## Реализация на C++

## Stack

```ShellSession
#include <stdio.h>

struct Stack
{
    using value_type = std::string;
    struct Node
    {
        value_type Data;
        Node* Next;
    };
    Node* Top;
};

#include "stack.hpp"

Stack* initStack() {
    Stack* st = new Stack();
    st->Top = nullptr;
    st->Bottom = nullptr;
    
    return st;
}

Stack::Node* crNodeStack(const Stack::value_type& data, Stack::Node* next) {
    Stack::Node* node = new Stack::Node();
    node->Data = data;
    node->Next = next;
    
    return node;
}

void dtrNodeStack(Stack::Node** ptr) {
    Stack::Node* node = *ptr;
    delete node;
    *ptr = nullptr;
}

void destroyStack(Stack** st) {
    Stack* stack = *st;
    Stack::Node* top = stack->Top;
    while (top != nullptr) {
        Stack::Node* tmp = top->Next;
        delete top;
        top = tmp;
    }
    delete stack;
    *st = nullptr;
}

Stack::Node* pushStack(Stack* const st, const Stack::value_type& data) {
    Stack::Node* node = crNodeStack(data, st->Top);
    if (st->Bottom == nullptr) {
        st->Bottom = node;
    }
    st->Top = node;
    
    return node;
}

void popStack(Stack* const st) {
    Stack::Node* tmp = st->Top->Next;
    dtrNodeStack(&st->Top);
    st->Top = tmp;
}

Stack::Node* top(Stack* const st) {
    auto bot = st->Top;
    return bot;
}
```

## Graph

```ShellSession
#pragma once
#include <algorithm>
#include <stdexcept>
#include <set>
#include <map>
#include <vector>

template <class CostType, class VertexId>
class Graph
{
 public:
    Graph()
       : EdgesSize(0)
    {}

    ~Graph()
    {}

    Graph& AddVertex(const VertexId& v)
    {
        auto it = std::find(vecVertex.begin(), vecVertex.end(), v);
        if (it != vecVertex.end())
            throw std::runtime_error("Error, item already exists");
        else
            vecVertex.push_back(v);
        return *this;
    }

    Graph& AddEdge(const VertexId& out, const VertexId& in, CostType cost)
    {
        vecAdjac.push_back(ListAdjacencies(out, in, cost));
        ++EdgesSize;
        return *this;
    }

    size_t VerteciesCount() const
    {
        return vecVertex.size();
    }

    size_t EdgesCount() const
    {
        return EdgesSize;
    }

    size_t VertexDegree(const VertexId& v) const
    {
        auto a = std::find(vecVertex.begin(), vecVertex.end(), v);
        if (a == vecVertex.end())
            throw std::runtime_error("Error, item already exists");
        size_t countDeg = 0;
        for (auto it = vecAdjac.begin(); it != vecAdjac.end(); ++it)
        {
            if ((v == it->Vert.first) || (v == it->Vert.second))
                ++countDeg;
        }
        return countDeg;
    }

    std::set<std::pair<VertexId, VertexId>> Edges() const
    {
        std::set<std::pair<VertexId, VertexId>> set;
        for (const auto a : vecAdjac)
            set.emplace(a.Vert);
        return set;
    }

    std::set<VertexId> Vertices() const
    {
        std::set<VertexId> set;
        for (const auto& a : vecAdjac)
        {
            set.emplace(a.Vert.first);
            set.emplace(a.Vert.second);
        }
        return set;
    }

    std::set<VertexId> AdjacentVertices(const VertexId& v) const
    {
        auto it = std::find(vecVertex.begin(), vecVertex.end(), v);
        if (it == vecVertex.end())
            throw std::runtime_error("Error, item already exists");
        std::set<VertexId> set;
        for (const auto& a : vecAdjac)
        {
            if (a.Vert.first == v)
                set.emplace(a.Vert.second);
            else if (a.Vert.second == v)
               set.emplace(a.Vert.first);
        }
        return set;
    }

    std::set<std::pair<VertexId, VertexId>> AdjacentEdges(const VertexId& v) const
    {
        auto it = std::find(vecVertex.begin(), vecVertex.end(), v);
        if (it == vecVertex.end())
            throw std::runtime_error("Error, item already exists");
        std::set<std::pair<VertexId, VertexId>> set;
        for (const auto& a : vecAdjac)
        {
            if ((a.Vert.first == v) && (a.Vert.second != v))
                set.emplace(a.Vert);
            else if ((a.Vert.first != v) && (a.Vert.second == v))
                set.emplace(a.Vert);
        }
        return set;
    }
 
    CostType Cost(const VertexId& out, const VertexId& in) const
    {
        CostType val = 0;
        for (const auto& a : vecAdjac)
            if ((a.Vert.first == out) && (a.Vert.second == in))
            {
                val = a.Cost;
                return val;
            }
        throw std::runtime_error("Error, there is no such way");
    }

 private:
    struct ListAdjacencies
    {
        std::pair<VertexId, VertexId> Vert;
        CostType Cost;
        ListAdjacencies(const VertexId& a, const VertexId& b, const CostType& cs)
            : Vert(a, b)
            , Cost(cs)
        {};
    };
    std::vector<VertexId> vecVertex;
    std::vector<ListAdjacencies> vecAdjac;
    size_t EdgesSize;
};

template <class T>
using Path = std::vector<T>;

template <class C, class T>
using Info = std::pair<C, Path<T>>;

template <class CostType, class VertexId>
std::map<VertexId, Info<CostType, VertexId>> Dijkstra(const Graph<CostType, VertexId>& gr,
    const VertexId& source);
```
## Forward list

```ShellSession

forward_list.h
#ifndef forward_list_hpp
#define forward_list_hpp

#include <stdio.h>
#include <iostream>

struct ForwardList {
    using value_type = int;
    
    struct Node {
        value_type Data;
        Node* Next;
    };
    
    Node* Head;
    Node* Tail;
};

ForwardList* initForwardList();

void destroyList(ForwardList**);

void dtrNodeList(ForwardList::Node**);

ForwardList::Node* crNodeList(const ForwardList::value_type&, ForwardList::Node*);

ForwardList::Node* pushFront(ForwardList* const, const ForwardList::value_type&);

void popFront(ForwardList* const);

ForwardList::Node* insertAfter(ForwardList* const, ForwardList::Node* const, const ForwardList::value_type&);

void eraseAfter(ForwardList* const, ForwardList::Node* const);

ForwardList::Node* reverse(ForwardList* const); 

size_t size(const ForwardList* const);

void print(const ForwardList* const);

#endif /* forward_list_hpp */

forward_list.cpp
#include "forward_list.hpp"

ForwardList* initForwardList() {
    ForwardList* list = new ForwardList();
    list->Head = nullptr;
    list->Tail = nullptr;
    
    return list;
}

void destroyList(ForwardList** lisd) {
    ForwardList* list = *lisd;
    ForwardList::Node* head = list->Head;
    while (head) {
        ForwardList::Node* tmp = head->Next;
        delete head;
        head = tmp;
    }
    delete list;
    *lisd = nullptr;
}

ForwardList::Node* crNodeList(const ForwardList::value_type& data, ForwardList::Node* next) {
    ForwardList::Node* node = new ForwardList::Node();
    node->Data = data;
    node->Next = next;
    
    return node;
}

void dtrNodeList(ForwardList::Node** ptr) {
    ForwardList::Node* node = *ptr;
    delete node;
    *ptr = nullptr;
}

ForwardList::Node* pushFront(ForwardList* const list, const ForwardList::value_type& data) {
    ForwardList::Node* node = crNodeList(data, list->Head);
    list->Head = node;
    if (list->Tail == nullptr)
        list->Tail = node;
    
    return node;
}

void popFront(ForwardList* const list) {
    ForwardList::Node* tmp = list->Head->Next;
    dtrNodeList(&list->Head);
    list->Head = tmp;
}

ForwardList::Node* insertAfter(ForwardList* const list, ForwardList::Node* const place, const ForwardList::value_type& data) {
    ForwardList::Node* node = crNodeList(data, place->Next);
    place->Next = node;
    
    return node;
}

void eraseAfter(ForwardList* const list, ForwardList::Node* const place) {
    ForwardList::Node* next = place->Next->Next;
    if (list->Head == place)
        list->Head = next;
    delete place->Next;
    place->Next = next;
}

ForwardList::Node* reverse(ForwardList* const list) {
    ForwardList::Node* prev = nullptr;
    ForwardList::Node* tmp = list->Head;
    ForwardList::Node* next = nullptr ;
    while (tmp != nullptr) {
        next = tmp->Next;
        tmp->Next = prev;
        prev = tmp;
        tmp = next;
    }
    list->Head = prev;
    
    return list->Head;
}

size_t size(const ForwardList* const list) {
    size_t size = 0;
    ForwardList::Node* head = list->Head;
    while (head != nullptr) {
        size += 1;
        head = head->Next;
    }
    
    return size;
}

void print(const ForwardList* const list) {
    ForwardList::Node* tmp = list->Head;
    while (tmp != nullptr) {
        std::cout << tmp->Data << std::endl;
        tmp = tmp->Next;
    }
}
```

## Queue

```ShellSession
#include <stdio.h>

struct Queue {
    using value_type = int;
    
    struct Node {
        value_type Data;
        Node* Next;
    };
    
    Node* Head;
    Node* Tail;
};

Queue* initQueue() {
    Queue* qu = new Queue();
    qu->Head = nullptr;
    qu->Tail = nullptr;
    
    return qu;
}

Queue::Node* crNodeQueue(const Queue::value_type& data, Queue::Node* next) {
    Queue::Node* node = new Queue::Node();
    node->Data = data;
    node->Next = next;
    
    return node;
}

void dtrNodeQueue(Queue::Node** ptr) {
    Queue::Node* node = *ptr;
    delete node;
    *ptr = nullptr;
}

void destroyQueue(Queue** queue) {
    Queue* qu = *queue;
    Queue::Node* head = qu->Head;
    while (head != nullptr) {
        Queue::Node* tmp = head->Next;
        delete head;
        head = tmp;
    }
    delete qu;
    *queue = nullptr;
}

Queue::Node* pushQueue(Queue* const qu, const Queue::value_type& data) {
    Queue::Node* node = crNodeQueue(data, qu->Head);
    qu->Head = node;
    if (qu->Tail == nullptr) {
        qu->Tail = node;
    }
    return node;
}

void popQueue(Queue* const qu) {
    Queue::Node* tmp = qu->Head->Next;
    dtrNodeQueue(&qu->Head);
    qu->Head = tmp;
}

Queue::Node* front(Queue* const qu) {
    auto tm = qu->Tail;
    return tm;
}

Queue::Node* back(Queue* const qu) {
    auto tm = qu->Head;
    return tm;
}
```

## Отображения 

```
// -*- C++ -*-
//===----------------------------- map ------------------------------------===//
//
//                     The LLVM Compiler Infrastructure
//
// This file is dual licensed under the MIT and the University of Illinois Open
// Source Licenses. See LICENSE.TXT for details.
//
//===----------------------------------------------------------------------===//

#ifndef _LIBCPP_MAP
#define _LIBCPP_MAP

/*

    map synopsis

namespace std
{

template <class Key, class T, class Compare = less<Key>,
          class Allocator = allocator<pair<const Key, T>>>
class map
{
public:
    // types:
    typedef Key                                      key_type;
    typedef T                                        mapped_type;
    typedef pair<const key_type, mapped_type>        value_type;
    typedef Compare                                  key_compare;
    typedef Allocator                                allocator_type;
    typedef typename allocator_type::reference       reference;
    typedef typename allocator_type::const_reference const_reference;
    typedef typename allocator_type::pointer         pointer;
    typedef typename allocator_type::const_pointer   const_pointer;
    typedef typename allocator_type::size_type       size_type;
    typedef typename allocator_type::difference_type difference_type;

    typedef implementation-defined                   iterator;
    typedef implementation-defined                   const_iterator;
    typedef std::reverse_iterator<iterator>          reverse_iterator;
    typedef std::reverse_iterator<const_iterator>    const_reverse_iterator;

    class value_compare
        : public binary_function<value_type, value_type, bool>
    {
        friend class map;
    protected:
        key_compare comp;

        value_compare(key_compare c);
    public:
        bool operator()(const value_type& x, const value_type& y) const;
    };

    // construct/copy/destroy:
    map()
        noexcept(
            is_nothrow_default_constructible<allocator_type>::value &&
            is_nothrow_default_constructible<key_compare>::value &&
            is_nothrow_copy_constructible<key_compare>::value);
    explicit map(const key_compare& comp);
    map(const key_compare& comp, const allocator_type& a);
    template <class InputIterator>
        map(InputIterator first, InputIterator last,
            const key_compare& comp = key_compare());
    template <class InputIterator>
        map(InputIterator first, InputIterator last,
            const key_compare& comp, const allocator_type& a);
    map(const map& m);
    map(map&& m)
        noexcept(
            is_nothrow_move_constructible<allocator_type>::value &&
            is_nothrow_move_constructible<key_compare>::value);
    explicit map(const allocator_type& a);
    map(const map& m, const allocator_type& a);
    map(map&& m, const allocator_type& a);
    map(initializer_list<value_type> il, const key_compare& comp = key_compare());
    map(initializer_list<value_type> il, const key_compare& comp, const allocator_type& a);
    template <class InputIterator>
        map(InputIterator first, InputIterator last, const allocator_type& a)
            : map(first, last, Compare(), a) {}  // C++14
    map(initializer_list<value_type> il, const allocator_type& a)
        : map(il, Compare(), a) {}  // C++14
   ~map();

    map& operator=(const map& m);
    map& operator=(map&& m)
        noexcept(
            allocator_type::propagate_on_container_move_assignment::value &&
            is_nothrow_move_assignable<allocator_type>::value &&
            is_nothrow_move_assignable<key_compare>::value);
    map& operator=(initializer_list<value_type> il);

    // iterators:
          iterator begin() noexcept;
    const_iterator begin() const noexcept;
          iterator end() noexcept;
    const_iterator end()   const noexcept;

          reverse_iterator rbegin() noexcept;
    const_reverse_iterator rbegin() const noexcept;
          reverse_iterator rend() noexcept;
    const_reverse_iterator rend()   const noexcept;

    const_iterator         cbegin()  const noexcept;
    const_iterator         cend()    const noexcept;
    const_reverse_iterator crbegin() const noexcept;
    const_reverse_iterator crend()   const noexcept;

    // capacity:
    bool      empty()    const noexcept;
    size_type size()     const noexcept;
    size_type max_size() const noexcept;

    // element access:
    mapped_type& operator[](const key_type& k);
    mapped_type& operator[](key_type&& k);

          mapped_type& at(const key_type& k);
    const mapped_type& at(const key_type& k) const;

    // modifiers:
    template <class... Args>
        pair<iterator, bool> emplace(Args&&... args);
    template <class... Args>
        iterator emplace_hint(const_iterator position, Args&&... args);
    pair<iterator, bool> insert(const value_type& v);
    pair<iterator, bool> insert(      value_type&& v);                                // C++17
    template <class P>
        pair<iterator, bool> insert(P&& p);
    iterator insert(const_iterator position, const value_type& v);
    iterator insert(const_iterator position,       value_type&& v);                   // C++17
    template <class P>
        iterator insert(const_iterator position, P&& p);
    template <class InputIterator>
        void insert(InputIterator first, InputIterator last);
    void insert(initializer_list<value_type> il);

    template <class... Args>
        pair<iterator, bool> try_emplace(const key_type& k, Args&&... args);          // C++17
    template <class... Args>
        pair<iterator, bool> try_emplace(key_type&& k, Args&&... args);               // C++17
    template <class... Args>
        iterator try_emplace(const_iterator hint, const key_type& k, Args&&... args); // C++17
    template <class... Args>
        iterator try_emplace(const_iterator hint, key_type&& k, Args&&... args);      // C++17
    template <class M>
        pair<iterator, bool> insert_or_assign(const key_type& k, M&& obj);            // C++17
    template <class M>
        pair<iterator, bool> insert_or_assign(key_type&& k, M&& obj);                 // C++17
    template <class M>
        iterator insert_or_assign(const_iterator hint, const key_type& k, M&& obj);   // C++17
    template <class M>
        iterator insert_or_assign(const_iterator hint, key_type&& k, M&& obj);        // C++17

    iterator  erase(const_iterator position);
    iterator  erase(iterator position); // C++14
    size_type erase(const key_type& k);
    iterator  erase(const_iterator first, const_iterator last);
    void clear() noexcept;

    void swap(map& m)
        noexcept(allocator_traits<allocator_type>::is_always_equal::value &&
            is_nothrow_swappable<key_compare>::value); // C++17

    // observers:
    allocator_type get_allocator() const noexcept;
    key_compare    key_comp()      const;
    value_compare  value_comp()    const;

    // map operations:
          iterator find(const key_type& k);
    const_iterator find(const key_type& k) const;
    template<typename K>
        iterator find(const K& x);              // C++14
    template<typename K>
        const_iterator find(const K& x) const;  // C++14
    template<typename K>
      size_type count(const K& x) const;        // C++14

    size_type      count(const key_type& k) const;
          iterator lower_bound(const key_type& k);
    const_iterator lower_bound(const key_type& k) const;
    template<typename K>
        iterator lower_bound(const K& x);              // C++14
    template<typename K>
        const_iterator lower_bound(const K& x) const;  // C++14

          iterator upper_bound(const key_type& k);
    const_iterator upper_bound(const key_type& k) const;
    template<typename K>
        iterator upper_bound(const K& x);              // C++14
    template<typename K>
        const_iterator upper_bound(const K& x) const;  // C++14

    pair<iterator,iterator>             equal_range(const key_type& k);
    pair<const_iterator,const_iterator> equal_range(const key_type& k) const;
    template<typename K>
        pair<iterator,iterator>             equal_range(const K& x);        // C++14
    template<typename K>
        pair<const_iterator,const_iterator> equal_range(const K& x) const;  // C++14
};

template <class Key, class T, class Compare, class Allocator>
bool
operator==(const map<Key, T, Compare, Allocator>& x,
           const map<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator< (const map<Key, T, Compare, Allocator>& x,
           const map<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator!=(const map<Key, T, Compare, Allocator>& x,
           const map<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator> (const map<Key, T, Compare, Allocator>& x,
           const map<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator>=(const map<Key, T, Compare, Allocator>& x,
           const map<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator<=(const map<Key, T, Compare, Allocator>& x,
           const map<Key, T, Compare, Allocator>& y);

// specialized algorithms:
template <class Key, class T, class Compare, class Allocator>
void
swap(map<Key, T, Compare, Allocator>& x, map<Key, T, Compare, Allocator>& y)
    noexcept(noexcept(x.swap(y)));

template <class Key, class T, class Compare = less<Key>,
          class Allocator = allocator<pair<const Key, T>>>
class multimap
{
public:
    // types:
    typedef Key                                      key_type;
    typedef T                                        mapped_type;
    typedef pair<const key_type,mapped_type>         value_type;
    typedef Compare                                  key_compare;
    typedef Allocator                                allocator_type;
    typedef typename allocator_type::reference       reference;
    typedef typename allocator_type::const_reference const_reference;
    typedef typename allocator_type::size_type       size_type;
    typedef typename allocator_type::difference_type difference_type;
    typedef typename allocator_type::pointer         pointer;
    typedef typename allocator_type::const_pointer   const_pointer;

    typedef implementation-defined                   iterator;
    typedef implementation-defined                   const_iterator;
    typedef std::reverse_iterator<iterator>          reverse_iterator;
    typedef std::reverse_iterator<const_iterator>    const_reverse_iterator;

    class value_compare
        : public binary_function<value_type,value_type,bool>
    {
        friend class multimap;
    protected:
        key_compare comp;
        value_compare(key_compare c);
    public:
        bool operator()(const value_type& x, const value_type& y) const;
    };

    // construct/copy/destroy:
    multimap()
        noexcept(
            is_nothrow_default_constructible<allocator_type>::value &&
            is_nothrow_default_constructible<key_compare>::value &&
            is_nothrow_copy_constructible<key_compare>::value);
    explicit multimap(const key_compare& comp);
    multimap(const key_compare& comp, const allocator_type& a);
    template <class InputIterator>
        multimap(InputIterator first, InputIterator last, const key_compare& comp);
    template <class InputIterator>
        multimap(InputIterator first, InputIterator last, const key_compare& comp,
                 const allocator_type& a);
    multimap(const multimap& m);
    multimap(multimap&& m)
        noexcept(
            is_nothrow_move_constructible<allocator_type>::value &&
            is_nothrow_move_constructible<key_compare>::value);
    explicit multimap(const allocator_type& a);
    multimap(const multimap& m, const allocator_type& a);
    multimap(multimap&& m, const allocator_type& a);
    multimap(initializer_list<value_type> il, const key_compare& comp = key_compare());
    multimap(initializer_list<value_type> il, const key_compare& comp,
             const allocator_type& a);
    template <class InputIterator>
        multimap(InputIterator first, InputIterator last, const allocator_type& a)
            : multimap(first, last, Compare(), a) {} // C++14
    multimap(initializer_list<value_type> il, const allocator_type& a)
        : multimap(il, Compare(), a) {} // C++14
    ~multimap();

    multimap& operator=(const multimap& m);
    multimap& operator=(multimap&& m)
        noexcept(
            allocator_type::propagate_on_container_move_assignment::value &&
            is_nothrow_move_assignable<allocator_type>::value &&
            is_nothrow_move_assignable<key_compare>::value);
    multimap& operator=(initializer_list<value_type> il);

    // iterators:
          iterator begin() noexcept;
    const_iterator begin() const noexcept;
          iterator end() noexcept;
    const_iterator end()   const noexcept;

          reverse_iterator rbegin() noexcept;
    const_reverse_iterator rbegin() const noexcept;
          reverse_iterator rend() noexcept;
    const_reverse_iterator rend()   const noexcept;

    const_iterator         cbegin()  const noexcept;
    const_iterator         cend()    const noexcept;
    const_reverse_iterator crbegin() const noexcept;
    const_reverse_iterator crend()   const noexcept;

    // capacity:
    bool      empty()    const noexcept;
    size_type size()     const noexcept;
    size_type max_size() const noexcept;

    // modifiers:
    template <class... Args>
        iterator emplace(Args&&... args);
    template <class... Args>
        iterator emplace_hint(const_iterator position, Args&&... args);
    iterator insert(const value_type& v);
    iterator insert(      value_type&& v);                                            // C++17
    template <class P>
        iterator insert(P&& p);
    iterator insert(const_iterator position, const value_type& v);
    iterator insert(const_iterator position,       value_type&& v);                   // C++17
    template <class P>
        iterator insert(const_iterator position, P&& p);
    template <class InputIterator>
        void insert(InputIterator first, InputIterator last);
    void insert(initializer_list<value_type> il);

    iterator  erase(const_iterator position);
    iterator  erase(iterator position); // C++14
    size_type erase(const key_type& k);
    iterator  erase(const_iterator first, const_iterator last);
    void clear() noexcept;

    void swap(multimap& m)
        noexcept(allocator_traits<allocator_type>::is_always_equal::value &&
            is_nothrow_swappable<key_compare>::value); // C++17

    // observers:
    allocator_type get_allocator() const noexcept;
    key_compare    key_comp()      const;
    value_compare  value_comp()    const;

    // map operations:
          iterator find(const key_type& k);
    const_iterator find(const key_type& k) const;
    template<typename K>
        iterator find(const K& x);              // C++14
    template<typename K>
        const_iterator find(const K& x) const;  // C++14
    template<typename K>
      size_type count(const K& x) const;        // C++14

    size_type      count(const key_type& k) const;
          iterator lower_bound(const key_type& k);
    const_iterator lower_bound(const key_type& k) const;
    template<typename K>
        iterator lower_bound(const K& x);              // C++14
    template<typename K>
        const_iterator lower_bound(const K& x) const;  // C++14

          iterator upper_bound(const key_type& k);
    const_iterator upper_bound(const key_type& k) const;
    template<typename K>
        iterator upper_bound(const K& x);              // C++14
    template<typename K>
        const_iterator upper_bound(const K& x) const;  // C++14

    pair<iterator,iterator>             equal_range(const key_type& k);
    pair<const_iterator,const_iterator> equal_range(const key_type& k) const;
    template<typename K>
        pair<iterator,iterator>             equal_range(const K& x);        // C++14
    template<typename K>
        pair<const_iterator,const_iterator> equal_range(const K& x) const;  // C++14
};

template <class Key, class T, class Compare, class Allocator>
bool
operator==(const multimap<Key, T, Compare, Allocator>& x,
           const multimap<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator< (const multimap<Key, T, Compare, Allocator>& x,
           const multimap<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator!=(const multimap<Key, T, Compare, Allocator>& x,
           const multimap<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator> (const multimap<Key, T, Compare, Allocator>& x,
           const multimap<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator>=(const multimap<Key, T, Compare, Allocator>& x,
           const multimap<Key, T, Compare, Allocator>& y);

template <class Key, class T, class Compare, class Allocator>
bool
operator<=(const multimap<Key, T, Compare, Allocator>& x,
           const multimap<Key, T, Compare, Allocator>& y);

// specialized algorithms:
template <class Key, class T, class Compare, class Allocator>
void
swap(multimap<Key, T, Compare, Allocator>& x,
     multimap<Key, T, Compare, Allocator>& y)
    noexcept(noexcept(x.swap(y)));

}  // std

*/

#include <__config>
#include <__tree>
#include <iterator>
#include <memory>
#include <utility>
#include <functional>
#include <initializer_list>
#include <type_traits>

#if !defined(_LIBCPP_HAS_NO_PRAGMA_SYSTEM_HEADER)
#pragma GCC system_header
#endif

_LIBCPP_BEGIN_NAMESPACE_STD

template <class _Key, class _CP, class _Compare,
          bool = is_empty<_Compare>::value && !__libcpp_is_final<_Compare>::value
         >
class __map_value_compare
    : private _Compare
{
public:
    _LIBCPP_INLINE_VISIBILITY
    __map_value_compare()
        _NOEXCEPT_(is_nothrow_default_constructible<_Compare>::value)
        : _Compare() {}
    _LIBCPP_INLINE_VISIBILITY
    __map_value_compare(_Compare c)
        _NOEXCEPT_(is_nothrow_copy_constructible<_Compare>::value)
        : _Compare(c) {}
    _LIBCPP_INLINE_VISIBILITY
    const _Compare& key_comp() const _NOEXCEPT {return *this;}
    _LIBCPP_INLINE_VISIBILITY
    bool operator()(const _CP& __x, const _CP& __y) const
        {return static_cast<const _Compare&>(*this)(__x.__cc.first, __y.__cc.first);}
    _LIBCPP_INLINE_VISIBILITY
    bool operator()(const _CP& __x, const _Key& __y) const
        {return static_cast<const _Compare&>(*this)(__x.__cc.first, __y);}
    _LIBCPP_INLINE_VISIBILITY
    bool operator()(const _Key& __x, const _CP& __y) const
        {return static_cast<const _Compare&>(*this)(__x, __y.__cc.first);}
    void swap(__map_value_compare&__y)
        _NOEXCEPT_(__is_nothrow_swappable<_Compare>::value)
    {
        using _VSTD::swap;
        swap(static_cast<const _Compare&>(*this), static_cast<const _Compare&>(__y));
    }

#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value, bool>::type
    operator () ( const _K2& __x, const _CP& __y ) const
        {return static_cast<const _Compare&>(*this) (__x, __y.__cc.first);}

    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value, bool>::type
    operator () (const _CP& __x, const _K2& __y) const
        {return static_cast<const _Compare&>(*this) (__x.__cc.first, __y);}
#endif
};

template <class _Key, class _CP, class _Compare>
class __map_value_compare<_Key, _CP, _Compare, false>
{
    _Compare comp;

public:
    _LIBCPP_INLINE_VISIBILITY
    __map_value_compare()
        _NOEXCEPT_(is_nothrow_default_constructible<_Compare>::value)
        : comp() {}
    _LIBCPP_INLINE_VISIBILITY
    __map_value_compare(_Compare c)
        _NOEXCEPT_(is_nothrow_copy_constructible<_Compare>::value)
        : comp(c) {}
    _LIBCPP_INLINE_VISIBILITY
    const _Compare& key_comp() const _NOEXCEPT {return comp;}

    _LIBCPP_INLINE_VISIBILITY
    bool operator()(const _CP& __x, const _CP& __y) const
        {return comp(__x.__cc.first, __y.__cc.first);}
    _LIBCPP_INLINE_VISIBILITY
    bool operator()(const _CP& __x, const _Key& __y) const
        {return comp(__x.__cc.first, __y);}
    _LIBCPP_INLINE_VISIBILITY
    bool operator()(const _Key& __x, const _CP& __y) const
        {return comp(__x, __y.__cc.first);}
    void swap(__map_value_compare&__y)
        _NOEXCEPT_(__is_nothrow_swappable<_Compare>::value)
    {
        using _VSTD::swap;
        swap(comp, __y.comp);
    }

#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value, bool>::type
    operator () ( const _K2& __x, const _CP& __y ) const
        {return comp (__x, __y.__cc.first);}

    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value, bool>::type
    operator () (const _CP& __x, const _K2& __y) const
        {return comp (__x.__cc.first, __y);}
#endif
};

template <class _Key, class _CP, class _Compare, bool __b>
inline _LIBCPP_INLINE_VISIBILITY
void
swap(__map_value_compare<_Key, _CP, _Compare, __b>& __x,
     __map_value_compare<_Key, _CP, _Compare, __b>& __y)
    _NOEXCEPT_(_NOEXCEPT_(__x.swap(__y)))
{
    __x.swap(__y);
}

template <class _Allocator>
class __map_node_destructor
{
    typedef _Allocator                          allocator_type;
    typedef allocator_traits<allocator_type>    __alloc_traits;

public:
    typedef typename __alloc_traits::pointer    pointer;

private:
    allocator_type& __na_;

    __map_node_destructor& operator=(const __map_node_destructor&);

public:
    bool __first_constructed;
    bool __second_constructed;

    _LIBCPP_INLINE_VISIBILITY
    explicit __map_node_destructor(allocator_type& __na) _NOEXCEPT
        : __na_(__na),
          __first_constructed(false),
          __second_constructed(false)
        {}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    __map_node_destructor(__tree_node_destructor<allocator_type>&& __x) _NOEXCEPT
        : __na_(__x.__na_),
          __first_constructed(__x.__value_constructed),
          __second_constructed(__x.__value_constructed)
        {
            __x.__value_constructed = false;
        }
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    void operator()(pointer __p) _NOEXCEPT
    {
        if (__second_constructed)
            __alloc_traits::destroy(__na_, _VSTD::addressof(__p->__value_.__cc.second));
        if (__first_constructed)
            __alloc_traits::destroy(__na_, _VSTD::addressof(__p->__value_.__cc.first));
        if (__p)
            __alloc_traits::deallocate(__na_, __p, 1);
    }
};

template <class _Key, class _Tp, class _Compare, class _Allocator>
    class map;
template <class _Key, class _Tp, class _Compare, class _Allocator>
    class multimap;
template <class _TreeIterator> class __map_const_iterator;

#ifndef _LIBCPP_CXX03_LANG

template <class _Key, class _Tp>
union __value_type
{
    typedef _Key                                     key_type;
    typedef _Tp                                      mapped_type;
    typedef pair<const key_type, mapped_type>        value_type;
    typedef pair<key_type, mapped_type>              __nc_value_type;

    value_type __cc;
    __nc_value_type __nc;

    _LIBCPP_INLINE_VISIBILITY
    __value_type& operator=(const __value_type& __v)
        {__nc = __v.__cc; return *this;}

    _LIBCPP_INLINE_VISIBILITY
    __value_type& operator=(__value_type&& __v)
        {__nc = _VSTD::move(__v.__nc); return *this;}

    template <class _ValueTp,
              class = typename enable_if<
                    __is_same_uncvref<_ValueTp, value_type>::value
                 >::type
             >
    _LIBCPP_INLINE_VISIBILITY
    __value_type& operator=(_ValueTp&& __v) {
        __nc = _VSTD::forward<_ValueTp>(__v); return *this;
    }

private:
    __value_type() _LIBCPP_EQUAL_DELETE;
    ~__value_type() _LIBCPP_EQUAL_DELETE;
    __value_type(const __value_type& __v) _LIBCPP_EQUAL_DELETE;
    __value_type(__value_type&& __v) _LIBCPP_EQUAL_DELETE;
};

#else

template <class _Key, class _Tp>
struct __value_type
{
    typedef _Key                                     key_type;
    typedef _Tp                                      mapped_type;
    typedef pair<const key_type, mapped_type>        value_type;

    value_type __cc;

private:
   __value_type();
   __value_type(__value_type const&);
   __value_type& operator=(__value_type const&);
   ~__value_type();
};

#endif

template <class _Tp>
struct __extract_key_value_types;

template <class _Key, class _Tp>
struct __extract_key_value_types<__value_type<_Key, _Tp> >
{
  typedef _Key const __key_type;
  typedef _Tp        __mapped_type;
};

template <class _TreeIterator>
class _LIBCPP_TEMPLATE_VIS __map_iterator
{
    typedef typename _TreeIterator::_NodeTypes                   _NodeTypes;
    typedef typename _TreeIterator::__pointer_traits             __pointer_traits;

    _TreeIterator __i_;

public:
    typedef bidirectional_iterator_tag                           iterator_category;
    typedef typename _NodeTypes::__map_value_type                value_type;
    typedef typename _TreeIterator::difference_type              difference_type;
    typedef value_type&                                          reference;
    typedef typename _NodeTypes::__map_value_type_pointer        pointer;

    _LIBCPP_INLINE_VISIBILITY
    __map_iterator() _NOEXCEPT {}

    _LIBCPP_INLINE_VISIBILITY
    __map_iterator(_TreeIterator __i) _NOEXCEPT : __i_(__i) {}

    _LIBCPP_INLINE_VISIBILITY
    reference operator*() const {return __i_->__cc;}
    _LIBCPP_INLINE_VISIBILITY
    pointer operator->() const {return pointer_traits<pointer>::pointer_to(__i_->__cc);}

    _LIBCPP_INLINE_VISIBILITY
    __map_iterator& operator++() {++__i_; return *this;}
    _LIBCPP_INLINE_VISIBILITY
    __map_iterator operator++(int)
    {
        __map_iterator __t(*this);
        ++(*this);
        return __t;
    }

    _LIBCPP_INLINE_VISIBILITY
    __map_iterator& operator--() {--__i_; return *this;}
    _LIBCPP_INLINE_VISIBILITY
    __map_iterator operator--(int)
    {
        __map_iterator __t(*this);
        --(*this);
        return __t;
    }

    friend _LIBCPP_INLINE_VISIBILITY
    bool operator==(const __map_iterator& __x, const __map_iterator& __y)
        {return __x.__i_ == __y.__i_;}
    friend
    _LIBCPP_INLINE_VISIBILITY
    bool operator!=(const __map_iterator& __x, const __map_iterator& __y)
        {return __x.__i_ != __y.__i_;}

    template <class, class, class, class> friend class _LIBCPP_TEMPLATE_VIS map;
    template <class, class, class, class> friend class _LIBCPP_TEMPLATE_VIS multimap;
    template <class> friend class _LIBCPP_TEMPLATE_VIS __map_const_iterator;
};

template <class _TreeIterator>
class _LIBCPP_TEMPLATE_VIS __map_const_iterator
{
    typedef typename _TreeIterator::_NodeTypes                   _NodeTypes;
    typedef typename _TreeIterator::__pointer_traits             __pointer_traits;

    _TreeIterator __i_;

public:
    typedef bidirectional_iterator_tag                           iterator_category;
    typedef typename _NodeTypes::__map_value_type                value_type;
    typedef typename _TreeIterator::difference_type              difference_type;
    typedef const value_type&                                    reference;
    typedef typename _NodeTypes::__const_map_value_type_pointer  pointer;

    _LIBCPP_INLINE_VISIBILITY
    __map_const_iterator() _NOEXCEPT {}

    _LIBCPP_INLINE_VISIBILITY
    __map_const_iterator(_TreeIterator __i) _NOEXCEPT : __i_(__i) {}
    _LIBCPP_INLINE_VISIBILITY
    __map_const_iterator(__map_iterator<
        typename _TreeIterator::__non_const_iterator> __i) _NOEXCEPT
        : __i_(__i.__i_) {}

    _LIBCPP_INLINE_VISIBILITY
    reference operator*() const {return __i_->__cc;}
    _LIBCPP_INLINE_VISIBILITY
    pointer operator->() const {return pointer_traits<pointer>::pointer_to(__i_->__cc);}

    _LIBCPP_INLINE_VISIBILITY
    __map_const_iterator& operator++() {++__i_; return *this;}
    _LIBCPP_INLINE_VISIBILITY
    __map_const_iterator operator++(int)
    {
        __map_const_iterator __t(*this);
        ++(*this);
        return __t;
    }

    _LIBCPP_INLINE_VISIBILITY
    __map_const_iterator& operator--() {--__i_; return *this;}
    _LIBCPP_INLINE_VISIBILITY
    __map_const_iterator operator--(int)
    {
        __map_const_iterator __t(*this);
        --(*this);
        return __t;
    }

    friend _LIBCPP_INLINE_VISIBILITY
    bool operator==(const __map_const_iterator& __x, const __map_const_iterator& __y)
        {return __x.__i_ == __y.__i_;}
    friend _LIBCPP_INLINE_VISIBILITY
    bool operator!=(const __map_const_iterator& __x, const __map_const_iterator& __y)
        {return __x.__i_ != __y.__i_;}

    template <class, class, class, class> friend class _LIBCPP_TEMPLATE_VIS map;
    template <class, class, class, class> friend class _LIBCPP_TEMPLATE_VIS multimap;
    template <class, class, class> friend class _LIBCPP_TEMPLATE_VIS __tree_const_iterator;
};

template <class _Key, class _Tp, class _Compare = less<_Key>,
          class _Allocator = allocator<pair<const _Key, _Tp> > >
class _LIBCPP_TEMPLATE_VIS map
{
public:
    // types:
    typedef _Key                                     key_type;
    typedef _Tp                                      mapped_type;
    typedef pair<const key_type, mapped_type>        value_type;
    typedef pair<key_type, mapped_type>              __nc_value_type;
    typedef _Compare                                 key_compare;
    typedef _Allocator                               allocator_type;
    typedef value_type&                              reference;
    typedef const value_type&                        const_reference;

    static_assert((is_same<typename allocator_type::value_type, value_type>::value),
                  "Allocator::value_type must be same type as value_type");

    class _LIBCPP_TEMPLATE_VIS value_compare
        : public binary_function<value_type, value_type, bool>
    {
        friend class map;
    protected:
        key_compare comp;

        _LIBCPP_INLINE_VISIBILITY value_compare(key_compare c) : comp(c) {}
    public:
        _LIBCPP_INLINE_VISIBILITY
        bool operator()(const value_type& __x, const value_type& __y) const
            {return comp(__x.first, __y.first);}
    };

private:

    typedef _VSTD::__value_type<key_type, mapped_type>             __value_type;
    typedef __map_value_compare<key_type, __value_type, key_compare> __vc;
    typedef typename __rebind_alloc_helper<allocator_traits<allocator_type>,
                                                 __value_type>::type __allocator_type;
    typedef __tree<__value_type, __vc, __allocator_type>   __base;
    typedef typename __base::__node_traits                 __node_traits;
    typedef allocator_traits<allocator_type>               __alloc_traits;

    __base __tree_;

public:
    typedef typename __alloc_traits::pointer               pointer;
    typedef typename __alloc_traits::const_pointer         const_pointer;
    typedef typename __alloc_traits::size_type             size_type;
    typedef typename __alloc_traits::difference_type       difference_type;
    typedef __map_iterator<typename __base::iterator>             iterator;
    typedef __map_const_iterator<typename __base::const_iterator> const_iterator;
    typedef _VSTD::reverse_iterator<iterator>               reverse_iterator;
    typedef _VSTD::reverse_iterator<const_iterator>         const_reverse_iterator;

    _LIBCPP_INLINE_VISIBILITY
    map()
        _NOEXCEPT_(
            is_nothrow_default_constructible<allocator_type>::value &&
            is_nothrow_default_constructible<key_compare>::value &&
            is_nothrow_copy_constructible<key_compare>::value)
        : __tree_(__vc(key_compare())) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit map(const key_compare& __comp)
        _NOEXCEPT_(
            is_nothrow_default_constructible<allocator_type>::value &&
            is_nothrow_copy_constructible<key_compare>::value)
        : __tree_(__vc(__comp)) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit map(const key_compare& __comp, const allocator_type& __a)
        : __tree_(__vc(__comp), typename __base::allocator_type(__a)) {}

    template <class _InputIterator>
    _LIBCPP_INLINE_VISIBILITY
        map(_InputIterator __f, _InputIterator __l,
            const key_compare& __comp = key_compare())
        : __tree_(__vc(__comp))
        {
            insert(__f, __l);
        }

    template <class _InputIterator>
    _LIBCPP_INLINE_VISIBILITY
        map(_InputIterator __f, _InputIterator __l,
            const key_compare& __comp, const allocator_type& __a)
        : __tree_(__vc(__comp), typename __base::allocator_type(__a))
        {
            insert(__f, __l);
        }

#if _LIBCPP_STD_VER > 11
    template <class _InputIterator>
    _LIBCPP_INLINE_VISIBILITY
    map(_InputIterator __f, _InputIterator __l, const allocator_type& __a)
        : map(__f, __l, key_compare(), __a) {}
#endif

    _LIBCPP_INLINE_VISIBILITY
    map(const map& __m)
        : __tree_(__m.__tree_)
        {
            insert(__m.begin(), __m.end());
        }

    _LIBCPP_INLINE_VISIBILITY
    map& operator=(const map& __m)
        {
#ifndef _LIBCPP_CXX03_LANG
            __tree_ = __m.__tree_;
#else
            if (this != &__m) {
                __tree_.clear();
                __tree_.value_comp() = __m.__tree_.value_comp();
                __tree_.__copy_assign_alloc(__m.__tree_);
                insert(__m.begin(), __m.end());
            }
#endif
            return *this;
        }

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    map(map&& __m)
        _NOEXCEPT_(is_nothrow_move_constructible<__base>::value)
        : __tree_(_VSTD::move(__m.__tree_))
        {
        }

    map(map&& __m, const allocator_type& __a);

    _LIBCPP_INLINE_VISIBILITY
    map& operator=(map&& __m)
        _NOEXCEPT_(is_nothrow_move_assignable<__base>::value)
        {
            __tree_ = _VSTD::move(__m.__tree_);
            return *this;
        }

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    map(initializer_list<value_type> __il, const key_compare& __comp = key_compare())
        : __tree_(__vc(__comp))
        {
            insert(__il.begin(), __il.end());
        }

    _LIBCPP_INLINE_VISIBILITY
    map(initializer_list<value_type> __il, const key_compare& __comp, const allocator_type& __a)
        : __tree_(__vc(__comp), typename __base::allocator_type(__a))
        {
            insert(__il.begin(), __il.end());
        }

#if _LIBCPP_STD_VER > 11
    _LIBCPP_INLINE_VISIBILITY
    map(initializer_list<value_type> __il, const allocator_type& __a)
        : map(__il, key_compare(), __a) {}
#endif

    _LIBCPP_INLINE_VISIBILITY
    map& operator=(initializer_list<value_type> __il)
        {
            __tree_.__assign_unique(__il.begin(), __il.end());
            return *this;
        }

#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    explicit map(const allocator_type& __a)
        : __tree_(typename __base::allocator_type(__a))
        {
        }

    _LIBCPP_INLINE_VISIBILITY
    map(const map& __m, const allocator_type& __a)
        : __tree_(__m.__tree_.value_comp(), typename __base::allocator_type(__a))
        {
            insert(__m.begin(), __m.end());
        }

    _LIBCPP_INLINE_VISIBILITY
          iterator begin() _NOEXCEPT {return __tree_.begin();}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator begin() const _NOEXCEPT {return __tree_.begin();}
    _LIBCPP_INLINE_VISIBILITY
          iterator end() _NOEXCEPT {return __tree_.end();}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator end() const _NOEXCEPT {return __tree_.end();}

    _LIBCPP_INLINE_VISIBILITY
          reverse_iterator rbegin() _NOEXCEPT {return reverse_iterator(end());}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator rbegin() const _NOEXCEPT
        {return const_reverse_iterator(end());}
    _LIBCPP_INLINE_VISIBILITY
          reverse_iterator rend() _NOEXCEPT
            {return       reverse_iterator(begin());}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator rend() const _NOEXCEPT
        {return const_reverse_iterator(begin());}

    _LIBCPP_INLINE_VISIBILITY
    const_iterator cbegin() const _NOEXCEPT {return begin();}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator cend() const _NOEXCEPT {return end();}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator crbegin() const _NOEXCEPT {return rbegin();}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator crend() const _NOEXCEPT {return rend();}

    _LIBCPP_INLINE_VISIBILITY
    bool      empty() const _NOEXCEPT {return __tree_.size() == 0;}
    _LIBCPP_INLINE_VISIBILITY
    size_type size() const _NOEXCEPT {return __tree_.size();}
    _LIBCPP_INLINE_VISIBILITY
    size_type max_size() const _NOEXCEPT {return __tree_.max_size();}

    mapped_type& operator[](const key_type& __k);
#ifndef _LIBCPP_CXX03_LANG
    mapped_type& operator[](key_type&& __k);
#endif

          mapped_type& at(const key_type& __k);
    const mapped_type& at(const key_type& __k) const;

    _LIBCPP_INLINE_VISIBILITY
    allocator_type get_allocator() const _NOEXCEPT {return allocator_type(__tree_.__alloc());}
    _LIBCPP_INLINE_VISIBILITY
    key_compare    key_comp()      const {return __tree_.value_comp().key_comp();}
    _LIBCPP_INLINE_VISIBILITY
    value_compare  value_comp()    const {return value_compare(__tree_.value_comp().key_comp());}

#ifndef _LIBCPP_CXX03_LANG
    template <class ..._Args>
    _LIBCPP_INLINE_VISIBILITY
    pair<iterator, bool> emplace(_Args&& ...__args) {
        return __tree_.__emplace_unique(_VSTD::forward<_Args>(__args)...);
    }

    template <class ..._Args>
    _LIBCPP_INLINE_VISIBILITY
    iterator emplace_hint(const_iterator __p, _Args&& ...__args) {
        return __tree_.__emplace_hint_unique(__p.__i_, _VSTD::forward<_Args>(__args)...);
    }

    template <class _Pp,
              class = typename enable_if<is_constructible<value_type, _Pp>::value>::type>
        _LIBCPP_INLINE_VISIBILITY
        pair<iterator, bool> insert(_Pp&& __p)
            {return __tree_.__insert_unique(_VSTD::forward<_Pp>(__p));}

    template <class _Pp,
              class = typename enable_if<is_constructible<value_type, _Pp>::value>::type>
        _LIBCPP_INLINE_VISIBILITY
        iterator insert(const_iterator __pos, _Pp&& __p)
            {return __tree_.__insert_unique(__pos.__i_, _VSTD::forward<_Pp>(__p));}

#endif  // _LIBCPP_CXX03_LANG

    _LIBCPP_INLINE_VISIBILITY
    pair<iterator, bool>
        insert(const value_type& __v) {return __tree_.__insert_unique(__v);}

    _LIBCPP_INLINE_VISIBILITY
    iterator
        insert(const_iterator __p, const value_type& __v)
            {return __tree_.__insert_unique(__p.__i_, __v);}

#ifndef _LIBCPP_CXX03_LANG
    _LIBCPP_INLINE_VISIBILITY
    pair<iterator, bool>
    insert(value_type&& __v) {return __tree_.__insert_unique(_VSTD::move(__v));}

    _LIBCPP_INLINE_VISIBILITY
    iterator insert(const_iterator __p,  value_type&& __v)
    {return __tree_.__insert_unique(__p.__i_, _VSTD::move(__v));}
#endif

    template <class _InputIterator>
        _LIBCPP_INLINE_VISIBILITY
        void insert(_InputIterator __f, _InputIterator __l)
        {
            for (const_iterator __e = cend(); __f != __l; ++__f)
                insert(__e.__i_, *__f);
        }

#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    void insert(initializer_list<value_type> __il)
        {insert(__il.begin(), __il.end());}

#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

#if _LIBCPP_STD_VER > 14

    template <class... _Args>
        _LIBCPP_INLINE_VISIBILITY
        pair<iterator, bool> try_emplace(const key_type& __k, _Args&&... __args)
    {
        return __tree_.__emplace_unique_key_args(__k,
            _VSTD::piecewise_construct,
            _VSTD::forward_as_tuple(__k),
            _VSTD::forward_as_tuple(_VSTD::forward<_Args>(__args)...));
    }

    template <class... _Args>
        _LIBCPP_INLINE_VISIBILITY
        pair<iterator, bool> try_emplace(key_type&& __k, _Args&&... __args)
    {
        return __tree_.__emplace_unique_key_args(__k,
            _VSTD::piecewise_construct,
            _VSTD::forward_as_tuple(_VSTD::move(__k)),
            _VSTD::forward_as_tuple(_VSTD::forward<_Args>(__args)...));
    }

    template <class... _Args>
        _LIBCPP_INLINE_VISIBILITY
        iterator try_emplace(const_iterator __h, const key_type& __k, _Args&&... __args)
    {
        return __tree_.__emplace_hint_unique_key_args(__h.__i_, __k,
            _VSTD::piecewise_construct,
            _VSTD::forward_as_tuple(__k),
            _VSTD::forward_as_tuple(_VSTD::forward<_Args>(__args)...));
    }

    template <class... _Args>
        _LIBCPP_INLINE_VISIBILITY
        iterator try_emplace(const_iterator __h, key_type&& __k, _Args&&... __args)
    {
        return __tree_.__emplace_hint_unique_key_args(__h.__i_, __k,
            _VSTD::piecewise_construct,
            _VSTD::forward_as_tuple(_VSTD::move(__k)),
            _VSTD::forward_as_tuple(_VSTD::forward<_Args>(__args)...));
    }

    template <class _Vp>
        _LIBCPP_INLINE_VISIBILITY
        pair<iterator, bool> insert_or_assign(const key_type& __k, _Vp&& __v)
    {
        iterator __p = lower_bound(__k);
        if ( __p != end() && !key_comp()(__k, __p->first))
        {
            __p->second = _VSTD::forward<_Vp>(__v);
            return _VSTD::make_pair(__p, false);
        }
        return _VSTD::make_pair(emplace_hint(__p, __k, _VSTD::forward<_Vp>(__v)), true);
    }

    template <class _Vp>
        _LIBCPP_INLINE_VISIBILITY
        pair<iterator, bool> insert_or_assign(key_type&& __k, _Vp&& __v)
    {
        iterator __p = lower_bound(__k);
        if ( __p != end() && !key_comp()(__k, __p->first))
        {
            __p->second = _VSTD::forward<_Vp>(__v);
            return _VSTD::make_pair(__p, false);
        }
        return _VSTD::make_pair(emplace_hint(__p, _VSTD::move(__k), _VSTD::forward<_Vp>(__v)), true);
    }

    template <class _Vp>
        _LIBCPP_INLINE_VISIBILITY
        iterator insert_or_assign(const_iterator __h, const key_type& __k, _Vp&& __v)
     {
        iterator __p = lower_bound(__k);
        if ( __p != end() && !key_comp()(__k, __p->first))
        {
            __p->second = _VSTD::forward<_Vp>(__v);
            return __p;
        }
        return emplace_hint(__h, __k, _VSTD::forward<_Vp>(__v));
     }

    template <class _Vp>
        _LIBCPP_INLINE_VISIBILITY
        iterator insert_or_assign(const_iterator __h, key_type&& __k, _Vp&& __v)
     {
        iterator __p = lower_bound(__k);
        if ( __p != end() && !key_comp()(__k, __p->first))
        {
            __p->second = _VSTD::forward<_Vp>(__v);
            return __p;
        }
        return emplace_hint(__h, _VSTD::move(__k), _VSTD::forward<_Vp>(__v));
     }

#endif

    _LIBCPP_INLINE_VISIBILITY
    iterator erase(const_iterator __p) {return __tree_.erase(__p.__i_);}
    _LIBCPP_INLINE_VISIBILITY
    iterator erase(iterator __p)       {return __tree_.erase(__p.__i_);}
    _LIBCPP_INLINE_VISIBILITY
    size_type erase(const key_type& __k)
        {return __tree_.__erase_unique(__k);}
    _LIBCPP_INLINE_VISIBILITY
    iterator  erase(const_iterator __f, const_iterator __l)
        {return __tree_.erase(__f.__i_, __l.__i_);}
    _LIBCPP_INLINE_VISIBILITY
    void clear() _NOEXCEPT {__tree_.clear();}

    _LIBCPP_INLINE_VISIBILITY
    void swap(map& __m)
        _NOEXCEPT_(__is_nothrow_swappable<__base>::value)
        {__tree_.swap(__m.__tree_);}

    _LIBCPP_INLINE_VISIBILITY
    iterator find(const key_type& __k)             {return __tree_.find(__k);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator find(const key_type& __k) const {return __tree_.find(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,iterator>::type
    find(const _K2& __k)                           {return __tree_.find(__k);}
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,const_iterator>::type
    find(const _K2& __k) const                     {return __tree_.find(__k);}
#endif

    _LIBCPP_INLINE_VISIBILITY
    size_type      count(const key_type& __k) const
        {return __tree_.__count_unique(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,size_type>::type
    count(const _K2& __k) const {return __tree_.__count_unique(__k);}
#endif
    _LIBCPP_INLINE_VISIBILITY
    iterator lower_bound(const key_type& __k)
        {return __tree_.lower_bound(__k);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator lower_bound(const key_type& __k) const
        {return __tree_.lower_bound(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,iterator>::type
    lower_bound(const _K2& __k)       {return __tree_.lower_bound(__k);}

    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,const_iterator>::type
    lower_bound(const _K2& __k) const {return __tree_.lower_bound(__k);}
#endif

    _LIBCPP_INLINE_VISIBILITY
    iterator upper_bound(const key_type& __k)
        {return __tree_.upper_bound(__k);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator upper_bound(const key_type& __k) const
        {return __tree_.upper_bound(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,iterator>::type
    upper_bound(const _K2& __k)       {return __tree_.upper_bound(__k);}
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,const_iterator>::type
    upper_bound(const _K2& __k) const {return __tree_.upper_bound(__k);}
#endif

    _LIBCPP_INLINE_VISIBILITY
    pair<iterator,iterator> equal_range(const key_type& __k)
        {return __tree_.__equal_range_unique(__k);}
    _LIBCPP_INLINE_VISIBILITY
    pair<const_iterator,const_iterator> equal_range(const key_type& __k) const
        {return __tree_.__equal_range_unique(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,pair<iterator,iterator>>::type
    equal_range(const _K2& __k)       {return __tree_.__equal_range_unique(__k);}
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,pair<const_iterator,const_iterator>>::type
    equal_range(const _K2& __k) const {return __tree_.__equal_range_unique(__k);}
#endif

private:
    typedef typename __base::__node                    __node;
    typedef typename __base::__node_allocator          __node_allocator;
    typedef typename __base::__node_pointer            __node_pointer;
    typedef typename __base::__node_base_pointer       __node_base_pointer;
    typedef typename __base::__parent_pointer          __parent_pointer;

    typedef __map_node_destructor<__node_allocator> _Dp;
    typedef unique_ptr<__node, _Dp> __node_holder;

#ifdef _LIBCPP_CXX03_LANG
    __node_holder __construct_node_with_key(const key_type& __k);
#endif
};


#ifndef _LIBCPP_CXX03_LANG

template <class _Key, class _Tp, class _Compare, class _Allocator>
map<_Key, _Tp, _Compare, _Allocator>::map(map&& __m, const allocator_type& __a)
    : __tree_(_VSTD::move(__m.__tree_), typename __base::allocator_type(__a))
{
    if (__a != __m.get_allocator())
    {
        const_iterator __e = cend();
        while (!__m.empty())
            __tree_.__insert_unique(__e.__i_,
                    _VSTD::move(__m.__tree_.remove(__m.begin().__i_)->__value_.__nc));
    }
}

#endif  // !_LIBCPP_CXX03_LANG


#ifdef _LIBCPP_CXX03_LANG

template <class _Key, class _Tp, class _Compare, class _Allocator>
typename map<_Key, _Tp, _Compare, _Allocator>::__node_holder
map<_Key, _Tp, _Compare, _Allocator>::__construct_node_with_key(const key_type& __k)
{
    __node_allocator& __na = __tree_.__node_alloc();
    __node_holder __h(__node_traits::allocate(__na, 1), _Dp(__na));
    __node_traits::construct(__na, _VSTD::addressof(__h->__value_.__cc.first), __k);
    __h.get_deleter().__first_constructed = true;
    __node_traits::construct(__na, _VSTD::addressof(__h->__value_.__cc.second));
    __h.get_deleter().__second_constructed = true;
    return _LIBCPP_EXPLICIT_MOVE(__h);  // explicitly moved for C++03
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
_Tp&
map<_Key, _Tp, _Compare, _Allocator>::operator[](const key_type& __k)
{
    __parent_pointer __parent;
    __node_base_pointer& __child = __tree_.__find_equal(__parent, __k);
    __node_pointer __r = static_cast<__node_pointer>(__child);
    if (__child == nullptr)
    {
        __node_holder __h = __construct_node_with_key(__k);
        __tree_.__insert_node_at(__parent, __child, static_cast<__node_base_pointer>(__h.get()));
        __r = __h.release();
    }
    return __r->__value_.__cc.second;
}

#else

template <class _Key, class _Tp, class _Compare, class _Allocator>
_Tp&
map<_Key, _Tp, _Compare, _Allocator>::operator[](const key_type& __k)
{
    return __tree_.__emplace_unique_key_args(__k,
        _VSTD::piecewise_construct,
        _VSTD::forward_as_tuple(__k),
        _VSTD::forward_as_tuple()).first->__cc.second;
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
_Tp&
map<_Key, _Tp, _Compare, _Allocator>::operator[](key_type&& __k)
{
    return __tree_.__emplace_unique_key_args(__k,
        _VSTD::piecewise_construct,
        _VSTD::forward_as_tuple(_VSTD::move(__k)),
        _VSTD::forward_as_tuple()).first->__cc.second;
}

#endif  // !_LIBCPP_CXX03_LANG

template <class _Key, class _Tp, class _Compare, class _Allocator>
_Tp&
map<_Key, _Tp, _Compare, _Allocator>::at(const key_type& __k)
{
    __parent_pointer __parent;
    __node_base_pointer& __child = __tree_.__find_equal(__parent, __k);
#ifndef _LIBCPP_NO_EXCEPTIONS
    if (__child == nullptr)
        throw out_of_range("map::at:  key not found");
#endif  // _LIBCPP_NO_EXCEPTIONS
    return static_cast<__node_pointer>(__child)->__value_.__cc.second;
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
const _Tp&
map<_Key, _Tp, _Compare, _Allocator>::at(const key_type& __k) const
{
    __parent_pointer __parent;
    __node_base_pointer __child = __tree_.__find_equal(__parent, __k);
#ifndef _LIBCPP_NO_EXCEPTIONS
    if (__child == nullptr)
        throw out_of_range("map::at:  key not found");
#endif  // _LIBCPP_NO_EXCEPTIONS
    return static_cast<__node_pointer>(__child)->__value_.__cc.second;
}


template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator==(const map<_Key, _Tp, _Compare, _Allocator>& __x,
           const map<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return __x.size() == __y.size() && _VSTD::equal(__x.begin(), __x.end(), __y.begin());
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator< (const map<_Key, _Tp, _Compare, _Allocator>& __x,
           const map<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return _VSTD::lexicographical_compare(__x.begin(), __x.end(), __y.begin(), __y.end());
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator!=(const map<_Key, _Tp, _Compare, _Allocator>& __x,
           const map<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return !(__x == __y);
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator> (const map<_Key, _Tp, _Compare, _Allocator>& __x,
           const map<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return __y < __x;
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator>=(const map<_Key, _Tp, _Compare, _Allocator>& __x,
           const map<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return !(__x < __y);
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator<=(const map<_Key, _Tp, _Compare, _Allocator>& __x,
           const map<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return !(__y < __x);
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
void
swap(map<_Key, _Tp, _Compare, _Allocator>& __x,
     map<_Key, _Tp, _Compare, _Allocator>& __y)
    _NOEXCEPT_(_NOEXCEPT_(__x.swap(__y)))
{
    __x.swap(__y);
}

template <class _Key, class _Tp, class _Compare = less<_Key>,
          class _Allocator = allocator<pair<const _Key, _Tp> > >
class _LIBCPP_TEMPLATE_VIS multimap
{
public:
    // types:
    typedef _Key                                     key_type;
    typedef _Tp                                      mapped_type;
    typedef pair<const key_type, mapped_type>        value_type;
    typedef pair<key_type, mapped_type>              __nc_value_type;
    typedef _Compare                                 key_compare;
    typedef _Allocator                               allocator_type;
    typedef value_type&                              reference;
    typedef const value_type&                        const_reference;

    static_assert((is_same<typename allocator_type::value_type, value_type>::value),
                  "Allocator::value_type must be same type as value_type");

    class _LIBCPP_TEMPLATE_VIS value_compare
        : public binary_function<value_type, value_type, bool>
    {
        friend class multimap;
    protected:
        key_compare comp;

        _LIBCPP_INLINE_VISIBILITY
        value_compare(key_compare c) : comp(c) {}
    public:
        _LIBCPP_INLINE_VISIBILITY
        bool operator()(const value_type& __x, const value_type& __y) const
            {return comp(__x.first, __y.first);}
    };

private:

    typedef _VSTD::__value_type<key_type, mapped_type>             __value_type;
    typedef __map_value_compare<key_type, __value_type, key_compare> __vc;
    typedef typename __rebind_alloc_helper<allocator_traits<allocator_type>,
                                                 __value_type>::type __allocator_type;
    typedef __tree<__value_type, __vc, __allocator_type>            __base;
    typedef typename __base::__node_traits                          __node_traits;
    typedef allocator_traits<allocator_type>                        __alloc_traits;

    __base __tree_;

public:
    typedef typename __alloc_traits::pointer               pointer;
    typedef typename __alloc_traits::const_pointer         const_pointer;
    typedef typename __alloc_traits::size_type             size_type;
    typedef typename __alloc_traits::difference_type       difference_type;
    typedef __map_iterator<typename __base::iterator>      iterator;
    typedef __map_const_iterator<typename __base::const_iterator> const_iterator;
    typedef _VSTD::reverse_iterator<iterator>               reverse_iterator;
    typedef _VSTD::reverse_iterator<const_iterator>         const_reverse_iterator;

    _LIBCPP_INLINE_VISIBILITY
    multimap()
        _NOEXCEPT_(
            is_nothrow_default_constructible<allocator_type>::value &&
            is_nothrow_default_constructible<key_compare>::value &&
            is_nothrow_copy_constructible<key_compare>::value)
        : __tree_(__vc(key_compare())) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit multimap(const key_compare& __comp)
        _NOEXCEPT_(
            is_nothrow_default_constructible<allocator_type>::value &&
            is_nothrow_copy_constructible<key_compare>::value)
        : __tree_(__vc(__comp)) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit multimap(const key_compare& __comp, const allocator_type& __a)
        : __tree_(__vc(__comp), typename __base::allocator_type(__a)) {}

    template <class _InputIterator>
        _LIBCPP_INLINE_VISIBILITY
        multimap(_InputIterator __f, _InputIterator __l,
            const key_compare& __comp = key_compare())
        : __tree_(__vc(__comp))
        {
            insert(__f, __l);
        }

    template <class _InputIterator>
        _LIBCPP_INLINE_VISIBILITY
        multimap(_InputIterator __f, _InputIterator __l,
            const key_compare& __comp, const allocator_type& __a)
        : __tree_(__vc(__comp), typename __base::allocator_type(__a))
        {
            insert(__f, __l);
        }

#if _LIBCPP_STD_VER > 11
    template <class _InputIterator>
    _LIBCPP_INLINE_VISIBILITY
    multimap(_InputIterator __f, _InputIterator __l, const allocator_type& __a)
        : multimap(__f, __l, key_compare(), __a) {}
#endif

    _LIBCPP_INLINE_VISIBILITY
    multimap(const multimap& __m)
        : __tree_(__m.__tree_.value_comp(),
          __alloc_traits::select_on_container_copy_construction(__m.__tree_.__alloc()))
        {
            insert(__m.begin(), __m.end());
        }

    _LIBCPP_INLINE_VISIBILITY
    multimap& operator=(const multimap& __m)
        {
#ifndef _LIBCPP_CXX03_LANG
            __tree_ = __m.__tree_;
#else
            if (this != &__m) {
                __tree_.clear();
                __tree_.value_comp() = __m.__tree_.value_comp();
                __tree_.__copy_assign_alloc(__m.__tree_);
                insert(__m.begin(), __m.end());
            }
#endif
            return *this;
        }

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    multimap(multimap&& __m)
        _NOEXCEPT_(is_nothrow_move_constructible<__base>::value)
        : __tree_(_VSTD::move(__m.__tree_))
        {
        }

    multimap(multimap&& __m, const allocator_type& __a);

    _LIBCPP_INLINE_VISIBILITY
    multimap& operator=(multimap&& __m)
        _NOEXCEPT_(is_nothrow_move_assignable<__base>::value)
        {
            __tree_ = _VSTD::move(__m.__tree_);
            return *this;
        }

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    multimap(initializer_list<value_type> __il, const key_compare& __comp = key_compare())
        : __tree_(__vc(__comp))
        {
            insert(__il.begin(), __il.end());
        }

    _LIBCPP_INLINE_VISIBILITY
    multimap(initializer_list<value_type> __il, const key_compare& __comp, const allocator_type& __a)
        : __tree_(__vc(__comp), typename __base::allocator_type(__a))
        {
            insert(__il.begin(), __il.end());
        }

#if _LIBCPP_STD_VER > 11
    _LIBCPP_INLINE_VISIBILITY
    multimap(initializer_list<value_type> __il, const allocator_type& __a)
        : multimap(__il, key_compare(), __a) {}
#endif

    _LIBCPP_INLINE_VISIBILITY
    multimap& operator=(initializer_list<value_type> __il)
        {
            __tree_.__assign_multi(__il.begin(), __il.end());
            return *this;
        }

#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    explicit multimap(const allocator_type& __a)
        : __tree_(typename __base::allocator_type(__a))
        {
        }

    _LIBCPP_INLINE_VISIBILITY
    multimap(const multimap& __m, const allocator_type& __a)
        : __tree_(__m.__tree_.value_comp(), typename __base::allocator_type(__a))
        {
            insert(__m.begin(), __m.end());
        }

    _LIBCPP_INLINE_VISIBILITY
          iterator begin() _NOEXCEPT {return __tree_.begin();}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator begin() const _NOEXCEPT {return __tree_.begin();}
    _LIBCPP_INLINE_VISIBILITY
          iterator end() _NOEXCEPT {return __tree_.end();}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator end() const _NOEXCEPT {return __tree_.end();}

    _LIBCPP_INLINE_VISIBILITY
          reverse_iterator rbegin() _NOEXCEPT {return reverse_iterator(end());}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator rbegin() const _NOEXCEPT
        {return const_reverse_iterator(end());}
    _LIBCPP_INLINE_VISIBILITY
          reverse_iterator rend() _NOEXCEPT {return reverse_iterator(begin());}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator rend() const _NOEXCEPT
        {return const_reverse_iterator(begin());}

    _LIBCPP_INLINE_VISIBILITY
    const_iterator cbegin()  const _NOEXCEPT {return begin();}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator cend() const _NOEXCEPT {return end();}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator crbegin() const _NOEXCEPT {return rbegin();}
    _LIBCPP_INLINE_VISIBILITY
    const_reverse_iterator crend() const _NOEXCEPT {return rend();}

    _LIBCPP_INLINE_VISIBILITY
    bool empty() const _NOEXCEPT {return __tree_.size() == 0;}
    _LIBCPP_INLINE_VISIBILITY
    size_type size() const _NOEXCEPT {return __tree_.size();}
    _LIBCPP_INLINE_VISIBILITY
    size_type max_size() const _NOEXCEPT {return __tree_.max_size();}

    _LIBCPP_INLINE_VISIBILITY
    allocator_type get_allocator() const _NOEXCEPT {return allocator_type(__tree_.__alloc());}
    _LIBCPP_INLINE_VISIBILITY
    key_compare    key_comp() const {return __tree_.value_comp().key_comp();}
    _LIBCPP_INLINE_VISIBILITY
    value_compare  value_comp() const
        {return value_compare(__tree_.value_comp().key_comp());}

#ifndef _LIBCPP_CXX03_LANG

    template <class ..._Args>
    _LIBCPP_INLINE_VISIBILITY
    iterator emplace(_Args&& ...__args) {
        return __tree_.__emplace_multi(_VSTD::forward<_Args>(__args)...);
    }

    template <class ..._Args>
    _LIBCPP_INLINE_VISIBILITY
    iterator emplace_hint(const_iterator __p, _Args&& ...__args) {
        return __tree_.__emplace_hint_multi(__p.__i_, _VSTD::forward<_Args>(__args)...);
    }

    template <class _Pp,
              class = typename enable_if<is_constructible<value_type, _Pp>::value>::type>
        _LIBCPP_INLINE_VISIBILITY
        iterator insert(_Pp&& __p)
            {return __tree_.__insert_multi(_VSTD::forward<_Pp>(__p));}

    template <class _Pp,
              class = typename enable_if<is_constructible<value_type, _Pp>::value>::type>
        _LIBCPP_INLINE_VISIBILITY
        iterator insert(const_iterator __pos, _Pp&& __p)
            {return __tree_.__insert_multi(__pos.__i_, _VSTD::forward<_Pp>(__p));}

    _LIBCPP_INLINE_VISIBILITY
    iterator insert(value_type&& __v)
        {return __tree_.__insert_multi(_VSTD::move(__v));}

    _LIBCPP_INLINE_VISIBILITY
    iterator insert(const_iterator __p, value_type&& __v)
        {return __tree_.__insert_multi(__p.__i_, _VSTD::move(__v));}

#endif  // _LIBCPP_CXX03_LANG

    _LIBCPP_INLINE_VISIBILITY
    iterator insert(const value_type& __v) {return __tree_.__insert_multi(__v);}

    _LIBCPP_INLINE_VISIBILITY
    iterator insert(const_iterator __p, const value_type& __v)
            {return __tree_.__insert_multi(__p.__i_, __v);}

    template <class _InputIterator>
        _LIBCPP_INLINE_VISIBILITY
        void insert(_InputIterator __f, _InputIterator __l)
        {
            for (const_iterator __e = cend(); __f != __l; ++__f)
                __tree_.__insert_multi(__e.__i_, *__f);
        }

#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    void insert(initializer_list<value_type> __il)
        {insert(__il.begin(), __il.end());}

#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    iterator erase(const_iterator __p) {return __tree_.erase(__p.__i_);}
    _LIBCPP_INLINE_VISIBILITY
    iterator erase(iterator __p)       {return __tree_.erase(__p.__i_);}
    _LIBCPP_INLINE_VISIBILITY
    size_type erase(const key_type& __k) {return __tree_.__erase_multi(__k);}
    _LIBCPP_INLINE_VISIBILITY
    iterator  erase(const_iterator __f, const_iterator __l)
        {return __tree_.erase(__f.__i_, __l.__i_);}
    _LIBCPP_INLINE_VISIBILITY
    void clear() {__tree_.clear();}

    _LIBCPP_INLINE_VISIBILITY
    void swap(multimap& __m)
        _NOEXCEPT_(__is_nothrow_swappable<__base>::value)
        {__tree_.swap(__m.__tree_);}

    _LIBCPP_INLINE_VISIBILITY
    iterator find(const key_type& __k)             {return __tree_.find(__k);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator find(const key_type& __k) const {return __tree_.find(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,iterator>::type
    find(const _K2& __k)                           {return __tree_.find(__k);}
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,const_iterator>::type
    find(const _K2& __k) const                     {return __tree_.find(__k);}
#endif

    _LIBCPP_INLINE_VISIBILITY
    size_type      count(const key_type& __k) const
        {return __tree_.__count_multi(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,size_type>::type
    count(const _K2& __k) const {return __tree_.__count_multi(__k);}
#endif
    _LIBCPP_INLINE_VISIBILITY
    iterator lower_bound(const key_type& __k)
        {return __tree_.lower_bound(__k);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator lower_bound(const key_type& __k) const
            {return __tree_.lower_bound(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,iterator>::type
    lower_bound(const _K2& __k)       {return __tree_.lower_bound(__k);}

    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,const_iterator>::type
    lower_bound(const _K2& __k) const {return __tree_.lower_bound(__k);}
#endif

    _LIBCPP_INLINE_VISIBILITY
    iterator upper_bound(const key_type& __k)
            {return __tree_.upper_bound(__k);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator upper_bound(const key_type& __k) const
            {return __tree_.upper_bound(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,iterator>::type
    upper_bound(const _K2& __k)       {return __tree_.upper_bound(__k);}
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,const_iterator>::type
    upper_bound(const _K2& __k) const {return __tree_.upper_bound(__k);}
#endif

    _LIBCPP_INLINE_VISIBILITY
    pair<iterator,iterator>             equal_range(const key_type& __k)
            {return __tree_.__equal_range_multi(__k);}
    _LIBCPP_INLINE_VISIBILITY
    pair<const_iterator,const_iterator> equal_range(const key_type& __k) const
            {return __tree_.__equal_range_multi(__k);}
#if _LIBCPP_STD_VER > 11
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,pair<iterator,iterator>>::type
    equal_range(const _K2& __k)       {return __tree_.__equal_range_multi(__k);}
    template <typename _K2>
    _LIBCPP_INLINE_VISIBILITY
    typename enable_if<__is_transparent<_Compare, _K2>::value,pair<const_iterator,const_iterator>>::type
    equal_range(const _K2& __k) const {return __tree_.__equal_range_multi(__k);}
#endif

private:
    typedef typename __base::__node                    __node;
    typedef typename __base::__node_allocator          __node_allocator;
    typedef typename __base::__node_pointer            __node_pointer;

    typedef __map_node_destructor<__node_allocator> _Dp;
    typedef unique_ptr<__node, _Dp> __node_holder;
};

#ifndef _LIBCPP_CXX03_LANG
template <class _Key, class _Tp, class _Compare, class _Allocator>
multimap<_Key, _Tp, _Compare, _Allocator>::multimap(multimap&& __m, const allocator_type& __a)
    : __tree_(_VSTD::move(__m.__tree_), typename __base::allocator_type(__a))
{
    if (__a != __m.get_allocator())
    {
        const_iterator __e = cend();
        while (!__m.empty())
            __tree_.__insert_multi(__e.__i_,
                    _VSTD::move(__m.__tree_.remove(__m.begin().__i_)->__value_.__nc));
    }
}
#endif

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator==(const multimap<_Key, _Tp, _Compare, _Allocator>& __x,
           const multimap<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return __x.size() == __y.size() && _VSTD::equal(__x.begin(), __x.end(), __y.begin());
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator< (const multimap<_Key, _Tp, _Compare, _Allocator>& __x,
           const multimap<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return _VSTD::lexicographical_compare(__x.begin(), __x.end(), __y.begin(), __y.end());
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator!=(const multimap<_Key, _Tp, _Compare, _Allocator>& __x,
           const multimap<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return !(__x == __y);
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator> (const multimap<_Key, _Tp, _Compare, _Allocator>& __x,
           const multimap<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return __y < __x;
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator>=(const multimap<_Key, _Tp, _Compare, _Allocator>& __x,
           const multimap<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return !(__x < __y);
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator<=(const multimap<_Key, _Tp, _Compare, _Allocator>& __x,
           const multimap<_Key, _Tp, _Compare, _Allocator>& __y)
{
    return !(__y < __x);
}

template <class _Key, class _Tp, class _Compare, class _Allocator>
inline _LIBCPP_INLINE_VISIBILITY
void
swap(multimap<_Key, _Tp, _Compare, _Allocator>& __x,
     multimap<_Key, _Tp, _Compare, _Allocator>& __y)
    _NOEXCEPT_(_NOEXCEPT_(__x.swap(__y)))
{
    __x.swap(__y);
}

_LIBCPP_END_NAMESPACE_STD

#endif  // _LIBCPP_MAP
```

## Официальная реализация forward_list

```
// -*- C++ -*-
//===----------------------- forward_list ---------------------------------===//
//
//                     The LLVM Compiler Infrastructure
//
// This file is dual licensed under the MIT and the University of Illinois Open
// Source Licenses. See LICENSE.TXT for details.
//
//===----------------------------------------------------------------------===//

#ifndef _LIBCPP_FORWARD_LIST
#define _LIBCPP_FORWARD_LIST

/*
    forward_list synopsis

namespace std
{

template <class T, class Allocator = allocator<T>>
class forward_list
{
public:
    typedef T         value_type;
    typedef Allocator allocator_type;

    typedef value_type&                                                reference;
    typedef const value_type&                                          const_reference;
    typedef typename allocator_traits<allocator_type>::pointer         pointer;
    typedef typename allocator_traits<allocator_type>::const_pointer   const_pointer;
    typedef typename allocator_traits<allocator_type>::size_type       size_type;
    typedef typename allocator_traits<allocator_type>::difference_type difference_type;

    typedef <details> iterator;
    typedef <details> const_iterator;

    forward_list()
        noexcept(is_nothrow_default_constructible<allocator_type>::value);
    explicit forward_list(const allocator_type& a);
    explicit forward_list(size_type n);
    explicit forward_list(size_type n, const allocator_type& a); // C++14
    forward_list(size_type n, const value_type& v);
    forward_list(size_type n, const value_type& v, const allocator_type& a);
    template <class InputIterator>
        forward_list(InputIterator first, InputIterator last);
    template <class InputIterator>
        forward_list(InputIterator first, InputIterator last, const allocator_type& a);
    forward_list(const forward_list& x);
    forward_list(const forward_list& x, const allocator_type& a);
    forward_list(forward_list&& x)
        noexcept(is_nothrow_move_constructible<allocator_type>::value);
    forward_list(forward_list&& x, const allocator_type& a);
    forward_list(initializer_list<value_type> il);
    forward_list(initializer_list<value_type> il, const allocator_type& a);

    ~forward_list();

    forward_list& operator=(const forward_list& x);
    forward_list& operator=(forward_list&& x)
        noexcept(
             allocator_type::propagate_on_container_move_assignment::value &&
             is_nothrow_move_assignable<allocator_type>::value);
    forward_list& operator=(initializer_list<value_type> il);

    template <class InputIterator>
        void assign(InputIterator first, InputIterator last);
    void assign(size_type n, const value_type& v);
    void assign(initializer_list<value_type> il);

    allocator_type get_allocator() const noexcept;

    iterator       begin() noexcept;
    const_iterator begin() const noexcept;
    iterator       end() noexcept;
    const_iterator end() const noexcept;

    const_iterator cbegin() const noexcept;
    const_iterator cend() const noexcept;

    iterator       before_begin() noexcept;
    const_iterator before_begin() const noexcept;
    const_iterator cbefore_begin() const noexcept;

    bool empty() const noexcept;
    size_type max_size() const noexcept;

    reference       front();
    const_reference front() const;

    template <class... Args> reference emplace_front(Args&&... args);  // reference in C++17
    void push_front(const value_type& v);
    void push_front(value_type&& v);

    void pop_front();

    template <class... Args>
        iterator emplace_after(const_iterator p, Args&&... args);
    iterator insert_after(const_iterator p, const value_type& v);
    iterator insert_after(const_iterator p, value_type&& v);
    iterator insert_after(const_iterator p, size_type n, const value_type& v);
    template <class InputIterator>
        iterator insert_after(const_iterator p,
                              InputIterator first, InputIterator last);
    iterator insert_after(const_iterator p, initializer_list<value_type> il);

    iterator erase_after(const_iterator p);
    iterator erase_after(const_iterator first, const_iterator last);

    void swap(forward_list& x)
        noexcept(allocator_traits<allocator_type>::is_always_equal::value);  // C++17

    void resize(size_type n);
    void resize(size_type n, const value_type& v);
    void clear() noexcept;

    void splice_after(const_iterator p, forward_list& x);
    void splice_after(const_iterator p, forward_list&& x);
    void splice_after(const_iterator p, forward_list& x, const_iterator i);
    void splice_after(const_iterator p, forward_list&& x, const_iterator i);
    void splice_after(const_iterator p, forward_list& x,
                      const_iterator first, const_iterator last);
    void splice_after(const_iterator p, forward_list&& x,
                      const_iterator first, const_iterator last);
    void remove(const value_type& v);
    template <class Predicate> void remove_if(Predicate pred);
    void unique();
    template <class BinaryPredicate> void unique(BinaryPredicate binary_pred);
    void merge(forward_list& x);
    void merge(forward_list&& x);
    template <class Compare> void merge(forward_list& x, Compare comp);
    template <class Compare> void merge(forward_list&& x, Compare comp);
    void sort();
    template <class Compare> void sort(Compare comp);
    void reverse() noexcept;
};

template <class T, class Allocator>
    bool operator==(const forward_list<T, Allocator>& x,
                    const forward_list<T, Allocator>& y);

template <class T, class Allocator>
    bool operator< (const forward_list<T, Allocator>& x,
                    const forward_list<T, Allocator>& y);

template <class T, class Allocator>
    bool operator!=(const forward_list<T, Allocator>& x,
                    const forward_list<T, Allocator>& y);

template <class T, class Allocator>
    bool operator> (const forward_list<T, Allocator>& x,
                    const forward_list<T, Allocator>& y);

template <class T, class Allocator>
    bool operator>=(const forward_list<T, Allocator>& x,
                    const forward_list<T, Allocator>& y);

template <class T, class Allocator>
    bool operator<=(const forward_list<T, Allocator>& x,
                    const forward_list<T, Allocator>& y);

template <class T, class Allocator>
    void swap(forward_list<T, Allocator>& x, forward_list<T, Allocator>& y)
         noexcept(noexcept(x.swap(y)));

}  // std

*/

#include <__config>

#include <initializer_list>
#include <memory>
#include <limits>
#include <iterator>
#include <algorithm>

#include <__undef_min_max>

#if !defined(_LIBCPP_HAS_NO_PRAGMA_SYSTEM_HEADER)
#pragma GCC system_header
#endif

_LIBCPP_BEGIN_NAMESPACE_STD

template <class _Tp, class _VoidPtr> struct __forward_list_node;
template <class _NodePtr> struct __forward_begin_node;


template <class>
struct __forward_list_node_value_type;

template <class _Tp, class _VoidPtr>
struct __forward_list_node_value_type<__forward_list_node<_Tp, _VoidPtr> > {
  typedef _Tp type;
};

template <class _NodePtr>
struct __forward_node_traits {

  typedef typename remove_cv<
        typename pointer_traits<_NodePtr>::element_type>::type  __node;
  typedef typename __forward_list_node_value_type<__node>::type __node_value_type;
  typedef _NodePtr                                              __node_pointer;
  typedef __forward_begin_node<_NodePtr>                        __begin_node;
  typedef typename __rebind_pointer<_NodePtr, __begin_node>::type
                                                                __begin_node_pointer;
  typedef typename __rebind_pointer<_NodePtr, void>::type       __void_pointer;

#if defined(_LIBCPP_ABI_FORWARD_LIST_REMOVE_NODE_POINTER_UB)
  typedef __begin_node_pointer __iter_node_pointer;
#else
  typedef typename conditional<
          is_pointer<__void_pointer>::value,
          __begin_node_pointer,
          __node_pointer
    >::type __iter_node_pointer;
#endif

  typedef typename conditional<
          is_same<__iter_node_pointer, __node_pointer>::value,
          __begin_node_pointer,
          __node_pointer
    >::type __non_iter_node_pointer;

  _LIBCPP_INLINE_VISIBILITY
  static __iter_node_pointer __as_iter_node(__iter_node_pointer __p) {
      return __p;
  }
  _LIBCPP_INLINE_VISIBILITY
  static __iter_node_pointer __as_iter_node(__non_iter_node_pointer __p) {
      return static_cast<__iter_node_pointer>(static_cast<__void_pointer>(__p));
  }
};

template <class _NodePtr>
struct __forward_begin_node
{
    typedef _NodePtr pointer;
    typedef typename __rebind_pointer<_NodePtr, __forward_begin_node>::type __begin_node_pointer;

    pointer __next_;

    _LIBCPP_INLINE_VISIBILITY __forward_begin_node() : __next_(nullptr) {}

    _LIBCPP_INLINE_VISIBILITY
    __begin_node_pointer __next_as_begin() const {
        return static_cast<__begin_node_pointer>(__next_);
    }
};

template <class _Tp, class _VoidPtr>
struct _LIBCPP_HIDDEN __begin_node_of
{
    typedef __forward_begin_node<
        typename __rebind_pointer<_VoidPtr, __forward_list_node<_Tp, _VoidPtr> >::type
    > type;
};

template <class _Tp, class _VoidPtr>
struct __forward_list_node
    : public __begin_node_of<_Tp, _VoidPtr>::type
{
    typedef _Tp value_type;

    value_type __value_;
};


template <class _Tp, class _Alloc = allocator<_Tp> > class _LIBCPP_TEMPLATE_VIS forward_list;
template<class _NodeConstPtr> class _LIBCPP_TEMPLATE_VIS __forward_list_const_iterator;

template <class _NodePtr>
class _LIBCPP_TEMPLATE_VIS __forward_list_iterator
{
    typedef __forward_node_traits<_NodePtr>         __traits;
    typedef typename __traits::__node_pointer       __node_pointer;
    typedef typename __traits::__begin_node_pointer __begin_node_pointer;
    typedef typename __traits::__iter_node_pointer  __iter_node_pointer;
    typedef typename __traits::__void_pointer       __void_pointer;

    __iter_node_pointer __ptr_;

    _LIBCPP_INLINE_VISIBILITY
    __begin_node_pointer __get_begin() const {
        return static_cast<__begin_node_pointer>(
                static_cast<__void_pointer>(__ptr_));
    }
    _LIBCPP_INLINE_VISIBILITY
    __node_pointer __get_unsafe_node_pointer() const {
        return static_cast<__node_pointer>(
                static_cast<__void_pointer>(__ptr_));
    }

    _LIBCPP_INLINE_VISIBILITY
    explicit __forward_list_iterator(nullptr_t) _NOEXCEPT : __ptr_(nullptr) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit __forward_list_iterator(__begin_node_pointer __p) _NOEXCEPT
        : __ptr_(__traits::__as_iter_node(__p)) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit __forward_list_iterator(__node_pointer __p) _NOEXCEPT
        : __ptr_(__traits::__as_iter_node(__p)) {}

    template<class, class> friend class _LIBCPP_TEMPLATE_VIS forward_list;
    template<class> friend class _LIBCPP_TEMPLATE_VIS __forward_list_const_iterator;

public:
    typedef forward_iterator_tag                              iterator_category;
    typedef typename __traits::__node_value_type              value_type;
    typedef value_type&                                       reference;
    typedef typename pointer_traits<__node_pointer>::difference_type
                                                              difference_type;
    typedef typename __rebind_pointer<__node_pointer, value_type>::type pointer;

    _LIBCPP_INLINE_VISIBILITY
    __forward_list_iterator() _NOEXCEPT : __ptr_(nullptr) {}

    _LIBCPP_INLINE_VISIBILITY
    reference operator*() const {return __get_unsafe_node_pointer()->__value_;}
    _LIBCPP_INLINE_VISIBILITY
    pointer operator->() const {
        return pointer_traits<pointer>::pointer_to(__get_unsafe_node_pointer()->__value_);
    }

    _LIBCPP_INLINE_VISIBILITY
    __forward_list_iterator& operator++()
    {
        __ptr_ = __traits::__as_iter_node(__ptr_->__next_);
        return *this;
    }
    _LIBCPP_INLINE_VISIBILITY
    __forward_list_iterator operator++(int)
    {
        __forward_list_iterator __t(*this);
        ++(*this);
        return __t;
    }

    friend _LIBCPP_INLINE_VISIBILITY
    bool operator==(const __forward_list_iterator& __x,
                    const __forward_list_iterator& __y)
        {return __x.__ptr_ == __y.__ptr_;}
    friend _LIBCPP_INLINE_VISIBILITY
    bool operator!=(const __forward_list_iterator& __x,
                    const __forward_list_iterator& __y)
        {return !(__x == __y);}
};

template <class _NodeConstPtr>
class _LIBCPP_TEMPLATE_VIS __forward_list_const_iterator
{
    static_assert((!is_const<typename pointer_traits<_NodeConstPtr>::element_type>::value), "");
    typedef _NodeConstPtr _NodePtr;

    typedef __forward_node_traits<_NodePtr>         __traits;
    typedef typename __traits::__node               __node;
    typedef typename __traits::__node_pointer       __node_pointer;
    typedef typename __traits::__begin_node_pointer __begin_node_pointer;
    typedef typename __traits::__iter_node_pointer  __iter_node_pointer;
    typedef typename __traits::__void_pointer       __void_pointer;

    __iter_node_pointer __ptr_;

    __begin_node_pointer __get_begin() const {
        return static_cast<__begin_node_pointer>(
                static_cast<__void_pointer>(__ptr_));
    }
    __node_pointer __get_unsafe_node_pointer() const {
        return static_cast<__node_pointer>(
                static_cast<__void_pointer>(__ptr_));
    }

    _LIBCPP_INLINE_VISIBILITY
    explicit __forward_list_const_iterator(nullptr_t) _NOEXCEPT
        : __ptr_(nullptr) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit __forward_list_const_iterator(__begin_node_pointer __p) _NOEXCEPT
        : __ptr_(__traits::__as_iter_node(__p)) {}

    _LIBCPP_INLINE_VISIBILITY
    explicit __forward_list_const_iterator(__node_pointer __p) _NOEXCEPT
        : __ptr_(__traits::__as_iter_node(__p)) {}


    template<class, class> friend class forward_list;

public:
    typedef forward_iterator_tag                              iterator_category;
    typedef typename __traits::__node_value_type              value_type;
    typedef const value_type&                                 reference;
    typedef typename pointer_traits<__node_pointer>::difference_type
                                                              difference_type;
    typedef typename __rebind_pointer<__node_pointer, const value_type>::type
                                                              pointer;

    _LIBCPP_INLINE_VISIBILITY
    __forward_list_const_iterator() _NOEXCEPT : __ptr_(nullptr) {}
    _LIBCPP_INLINE_VISIBILITY
    __forward_list_const_iterator(__forward_list_iterator<__node_pointer> __p) _NOEXCEPT
        : __ptr_(__p.__ptr_) {}

    _LIBCPP_INLINE_VISIBILITY
    reference operator*() const {return __get_unsafe_node_pointer()->__value_;}
    _LIBCPP_INLINE_VISIBILITY
    pointer operator->() const {return pointer_traits<pointer>::pointer_to(
                __get_unsafe_node_pointer()->__value_);}

    _LIBCPP_INLINE_VISIBILITY
    __forward_list_const_iterator& operator++()
    {
        __ptr_ = __traits::__as_iter_node(__ptr_->__next_);
        return *this;
    }
    _LIBCPP_INLINE_VISIBILITY
    __forward_list_const_iterator operator++(int)
    {
        __forward_list_const_iterator __t(*this);
        ++(*this);
        return __t;
    }

    friend _LIBCPP_INLINE_VISIBILITY
    bool operator==(const __forward_list_const_iterator& __x,
                    const __forward_list_const_iterator& __y)
        {return __x.__ptr_ == __y.__ptr_;}
    friend _LIBCPP_INLINE_VISIBILITY
    bool operator!=(const __forward_list_const_iterator& __x,
                           const __forward_list_const_iterator& __y)
        {return !(__x == __y);}
};

template <class _Tp, class _Alloc>
class __forward_list_base
{
protected:
    typedef _Tp    value_type;
    typedef _Alloc allocator_type;

    typedef typename allocator_traits<allocator_type>::void_pointer  void_pointer;
    typedef __forward_list_node<value_type, void_pointer>            __node;
    typedef typename __begin_node_of<value_type, void_pointer>::type __begin_node;
    typedef typename __rebind_alloc_helper<allocator_traits<allocator_type>, __node>::type __node_allocator;
    typedef allocator_traits<__node_allocator>        __node_traits;
    typedef typename __node_traits::pointer           __node_pointer;

    typedef typename __rebind_alloc_helper<
        allocator_traits<allocator_type>, __begin_node
    >::type                                           __begin_node_allocator;
    typedef typename allocator_traits<__begin_node_allocator>::pointer
                                                      __begin_node_pointer;

    __compressed_pair<__begin_node, __node_allocator> __before_begin_;

    _LIBCPP_INLINE_VISIBILITY
    __begin_node_pointer        __before_begin() _NOEXCEPT
        {return pointer_traits<__begin_node_pointer>::pointer_to(__before_begin_.first());}
    _LIBCPP_INLINE_VISIBILITY
    __begin_node_pointer __before_begin() const _NOEXCEPT
        {return pointer_traits<__begin_node_pointer>::pointer_to(const_cast<__begin_node&>(__before_begin_.first()));}

    _LIBCPP_INLINE_VISIBILITY
          __node_allocator& __alloc() _NOEXCEPT
            {return __before_begin_.second();}
    _LIBCPP_INLINE_VISIBILITY
    const __node_allocator& __alloc() const _NOEXCEPT
        {return __before_begin_.second();}

    typedef __forward_list_iterator<__node_pointer>             iterator;
    typedef __forward_list_const_iterator<__node_pointer>       const_iterator;

    _LIBCPP_INLINE_VISIBILITY
    __forward_list_base()
        _NOEXCEPT_(is_nothrow_default_constructible<__node_allocator>::value)
        : __before_begin_(__begin_node()) {}
    _LIBCPP_INLINE_VISIBILITY
    __forward_list_base(const allocator_type& __a)
        : __before_begin_(__begin_node(), __node_allocator(__a)) {}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
public:
    _LIBCPP_INLINE_VISIBILITY
    __forward_list_base(__forward_list_base&& __x)
        _NOEXCEPT_(is_nothrow_move_constructible<__node_allocator>::value);
    _LIBCPP_INLINE_VISIBILITY
    __forward_list_base(__forward_list_base&& __x, const allocator_type& __a);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

private:
    __forward_list_base(const __forward_list_base&);
    __forward_list_base& operator=(const __forward_list_base&);

public:
    ~__forward_list_base();

protected:
    _LIBCPP_INLINE_VISIBILITY
    void __copy_assign_alloc(const __forward_list_base& __x)
        {__copy_assign_alloc(__x, integral_constant<bool,
              __node_traits::propagate_on_container_copy_assignment::value>());}

    _LIBCPP_INLINE_VISIBILITY
    void __move_assign_alloc(__forward_list_base& __x)
        _NOEXCEPT_(!__node_traits::propagate_on_container_move_assignment::value ||
                   is_nothrow_move_assignable<__node_allocator>::value)
        {__move_assign_alloc(__x, integral_constant<bool,
              __node_traits::propagate_on_container_move_assignment::value>());}

public:
    _LIBCPP_INLINE_VISIBILITY
    void swap(__forward_list_base& __x)
#if _LIBCPP_STD_VER >= 14
        _NOEXCEPT;
#else
        _NOEXCEPT_(!__node_traits::propagate_on_container_move_assignment::value || 
                    __is_nothrow_swappable<__node_allocator>::value);
#endif
protected:
    void clear() _NOEXCEPT;

private:
    _LIBCPP_INLINE_VISIBILITY
    void __copy_assign_alloc(const __forward_list_base&, false_type) {}
    _LIBCPP_INLINE_VISIBILITY
    void __copy_assign_alloc(const __forward_list_base& __x, true_type)
    {
        if (__alloc() != __x.__alloc())
            clear();
        __alloc() = __x.__alloc();
    }

    _LIBCPP_INLINE_VISIBILITY
    void __move_assign_alloc(__forward_list_base&, false_type) _NOEXCEPT
        {}
    _LIBCPP_INLINE_VISIBILITY
    void __move_assign_alloc(__forward_list_base& __x, true_type)
        _NOEXCEPT_(is_nothrow_move_assignable<__node_allocator>::value)
        {__alloc() = _VSTD::move(__x.__alloc());}
};

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
inline
__forward_list_base<_Tp, _Alloc>::__forward_list_base(__forward_list_base&& __x)
        _NOEXCEPT_(is_nothrow_move_constructible<__node_allocator>::value)
    : __before_begin_(_VSTD::move(__x.__before_begin_))
{
    __x.__before_begin()->__next_ = nullptr;
}

template <class _Tp, class _Alloc>
inline
__forward_list_base<_Tp, _Alloc>::__forward_list_base(__forward_list_base&& __x,
                                                      const allocator_type& __a)
    : __before_begin_(__begin_node(), __node_allocator(__a))
{
    if (__alloc() == __x.__alloc())
    {
        __before_begin()->__next_ = __x.__before_begin()->__next_;
        __x.__before_begin()->__next_ = nullptr;
    }
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
__forward_list_base<_Tp, _Alloc>::~__forward_list_base()
{
    clear();
}

template <class _Tp, class _Alloc>
inline
void
__forward_list_base<_Tp, _Alloc>::swap(__forward_list_base& __x)
#if _LIBCPP_STD_VER >= 14
        _NOEXCEPT
#else
        _NOEXCEPT_(!__node_traits::propagate_on_container_move_assignment::value || 
                    __is_nothrow_swappable<__node_allocator>::value)
#endif
{
    __swap_allocator(__alloc(), __x.__alloc(), 
            integral_constant<bool, __node_traits::propagate_on_container_swap::value>());
    using _VSTD::swap;
    swap(__before_begin()->__next_, __x.__before_begin()->__next_);
}

template <class _Tp, class _Alloc>
void
__forward_list_base<_Tp, _Alloc>::clear() _NOEXCEPT
{
    __node_allocator& __a = __alloc();
    for (__node_pointer __p = __before_begin()->__next_; __p != nullptr;)
    {
        __node_pointer __next = __p->__next_;
        __node_traits::destroy(__a, _VSTD::addressof(__p->__value_));
        __node_traits::deallocate(__a, __p, 1);
        __p = __next;
    }
    __before_begin()->__next_ = nullptr;
}

template <class _Tp, class _Alloc /*= allocator<_Tp>*/>
class _LIBCPP_TEMPLATE_VIS forward_list
    : private __forward_list_base<_Tp, _Alloc>
{
    typedef __forward_list_base<_Tp, _Alloc> base;
    typedef typename base::__node_allocator  __node_allocator;
    typedef typename base::__node               __node;
    typedef typename base::__node_traits        __node_traits;
    typedef typename base::__node_pointer       __node_pointer;
    typedef typename base::__begin_node_pointer __begin_node_pointer;

public:
    typedef _Tp    value_type;
    typedef _Alloc allocator_type;

    static_assert((is_same<typename allocator_type::value_type, value_type>::value),
                  "Allocator::value_type must be same type as value_type");

    typedef value_type&                                                reference;
    typedef const value_type&                                          const_reference;
    typedef typename allocator_traits<allocator_type>::pointer         pointer;
    typedef typename allocator_traits<allocator_type>::const_pointer   const_pointer;
    typedef typename allocator_traits<allocator_type>::size_type       size_type;
    typedef typename allocator_traits<allocator_type>::difference_type difference_type;

    typedef typename base::iterator       iterator;
    typedef typename base::const_iterator const_iterator;

    _LIBCPP_INLINE_VISIBILITY
    forward_list()
        _NOEXCEPT_(is_nothrow_default_constructible<__node_allocator>::value)
        {} // = default;
    _LIBCPP_INLINE_VISIBILITY
    explicit forward_list(const allocator_type& __a);
    explicit forward_list(size_type __n);
#if _LIBCPP_STD_VER > 11
    explicit forward_list(size_type __n, const allocator_type& __a);
#endif
    forward_list(size_type __n, const value_type& __v);
    forward_list(size_type __n, const value_type& __v, const allocator_type& __a);
    template <class _InputIterator>
        forward_list(_InputIterator __f, _InputIterator __l,
                     typename enable_if<
                       __is_input_iterator<_InputIterator>::value
                     >::type* = nullptr);
    template <class _InputIterator>
        forward_list(_InputIterator __f, _InputIterator __l,
                     const allocator_type& __a,
                     typename enable_if<
                       __is_input_iterator<_InputIterator>::value
                     >::type* = nullptr);
    forward_list(const forward_list& __x);
    forward_list(const forward_list& __x, const allocator_type& __a);
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    forward_list(forward_list&& __x)
        _NOEXCEPT_(is_nothrow_move_constructible<base>::value)
        : base(_VSTD::move(__x)) {}
    forward_list(forward_list&& __x, const allocator_type& __a);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS
    forward_list(initializer_list<value_type> __il);
    forward_list(initializer_list<value_type> __il, const allocator_type& __a);
#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    // ~forward_list() = default;

    forward_list& operator=(const forward_list& __x);
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    forward_list& operator=(forward_list&& __x)
        _NOEXCEPT_(
             __node_traits::propagate_on_container_move_assignment::value &&
             is_nothrow_move_assignable<allocator_type>::value);
#endif
#ifndef  _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS
    _LIBCPP_INLINE_VISIBILITY
    forward_list& operator=(initializer_list<value_type> __il);
#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    template <class _InputIterator>
        typename enable_if
        <
            __is_input_iterator<_InputIterator>::value,
            void
        >::type
        assign(_InputIterator __f, _InputIterator __l);
    void assign(size_type __n, const value_type& __v);
#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS
    _LIBCPP_INLINE_VISIBILITY
    void assign(initializer_list<value_type> __il);
#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    _LIBCPP_INLINE_VISIBILITY
    allocator_type get_allocator() const _NOEXCEPT
        {return allocator_type(base::__alloc());}

    _LIBCPP_INLINE_VISIBILITY
    iterator       begin() _NOEXCEPT
        {return       iterator(base::__before_begin()->__next_);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator begin() const _NOEXCEPT
        {return const_iterator(base::__before_begin()->__next_);}
    _LIBCPP_INLINE_VISIBILITY
    iterator       end() _NOEXCEPT
        {return       iterator(nullptr);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator end() const _NOEXCEPT
        {return const_iterator(nullptr);}

    _LIBCPP_INLINE_VISIBILITY
    const_iterator cbegin() const _NOEXCEPT
        {return const_iterator(base::__before_begin()->__next_);}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator cend() const _NOEXCEPT
        {return const_iterator(nullptr);}

    _LIBCPP_INLINE_VISIBILITY
    iterator       before_begin() _NOEXCEPT
        {return       iterator(base::__before_begin());}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator before_begin() const _NOEXCEPT
        {return const_iterator(base::__before_begin());}
    _LIBCPP_INLINE_VISIBILITY
    const_iterator cbefore_begin() const _NOEXCEPT
        {return const_iterator(base::__before_begin());}

    _LIBCPP_INLINE_VISIBILITY
    bool empty() const _NOEXCEPT
        {return base::__before_begin()->__next_ == nullptr;}
    _LIBCPP_INLINE_VISIBILITY
    size_type max_size() const _NOEXCEPT {
        return std::min<size_type>(
            __node_traits::max_size(base::__alloc()),
            numeric_limits<difference_type>::max());
    }

    _LIBCPP_INLINE_VISIBILITY
    reference       front()       {return base::__before_begin()->__next_->__value_;}
    _LIBCPP_INLINE_VISIBILITY
    const_reference front() const {return base::__before_begin()->__next_->__value_;}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
#ifndef _LIBCPP_HAS_NO_VARIADICS
#if _LIBCPP_STD_VER > 14
    template <class... _Args> reference emplace_front(_Args&&... __args);
#else
    template <class... _Args> void      emplace_front(_Args&&... __args);
#endif
#endif
    void push_front(value_type&& __v);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    void push_front(const value_type& __v);

    void pop_front();

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
#ifndef _LIBCPP_HAS_NO_VARIADICS
    template <class... _Args>
        iterator emplace_after(const_iterator __p, _Args&&... __args);
#endif  // _LIBCPP_HAS_NO_VARIADICS
    iterator insert_after(const_iterator __p, value_type&& __v);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    iterator insert_after(const_iterator __p, const value_type& __v);
    iterator insert_after(const_iterator __p, size_type __n, const value_type& __v);
    template <class _InputIterator>
        _LIBCPP_INLINE_VISIBILITY
        typename enable_if
        <
            __is_input_iterator<_InputIterator>::value,
            iterator
        >::type
        insert_after(const_iterator __p, _InputIterator __f, _InputIterator __l);
#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS
    iterator insert_after(const_iterator __p, initializer_list<value_type> __il)
        {return insert_after(__p, __il.begin(), __il.end());}
#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

    iterator erase_after(const_iterator __p);
    iterator erase_after(const_iterator __f, const_iterator __l);

    _LIBCPP_INLINE_VISIBILITY
    void swap(forward_list& __x)
#if _LIBCPP_STD_VER >= 14
        _NOEXCEPT
#else
        _NOEXCEPT_(!__node_traits::propagate_on_container_swap::value ||
                   __is_nothrow_swappable<__node_allocator>::value)
#endif
        {base::swap(__x);}

    void resize(size_type __n);
    void resize(size_type __n, const value_type& __v);
    _LIBCPP_INLINE_VISIBILITY
    void clear() _NOEXCEPT {base::clear();}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void splice_after(const_iterator __p, forward_list&& __x);
    _LIBCPP_INLINE_VISIBILITY
    void splice_after(const_iterator __p, forward_list&& __x, const_iterator __i);
    _LIBCPP_INLINE_VISIBILITY
    void splice_after(const_iterator __p, forward_list&& __x,
                      const_iterator __f, const_iterator __l);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    void splice_after(const_iterator __p, forward_list& __x);
    void splice_after(const_iterator __p, forward_list& __x, const_iterator __i);
    void splice_after(const_iterator __p, forward_list& __x,
                      const_iterator __f, const_iterator __l);
    void remove(const value_type& __v);
    template <class _Predicate> void remove_if(_Predicate __pred);
    _LIBCPP_INLINE_VISIBILITY
    void unique() {unique(__equal_to<value_type>());}
    template <class _BinaryPredicate> void unique(_BinaryPredicate __binary_pred);
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void merge(forward_list&& __x) {merge(__x, __less<value_type>());}
    template <class _Compare>
        _LIBCPP_INLINE_VISIBILITY
        void merge(forward_list&& __x, _Compare __comp)
        {merge(__x, _VSTD::move(__comp));}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void merge(forward_list& __x) {merge(__x, __less<value_type>());}
    template <class _Compare> void merge(forward_list& __x, _Compare __comp);
    _LIBCPP_INLINE_VISIBILITY
    void sort() {sort(__less<value_type>());}
    template <class _Compare> _LIBCPP_INLINE_VISIBILITY void sort(_Compare __comp);
    void reverse() _NOEXCEPT;

private:

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    void __move_assign(forward_list& __x, true_type)
        _NOEXCEPT_(is_nothrow_move_assignable<allocator_type>::value);
    void __move_assign(forward_list& __x, false_type);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    template <class _Compare>
        static
        __node_pointer
        __merge(__node_pointer __f1, __node_pointer __f2, _Compare& __comp);

    template <class _Compare>
        static
        __node_pointer
        __sort(__node_pointer __f, difference_type __sz, _Compare& __comp);
};

template <class _Tp, class _Alloc>
inline
forward_list<_Tp, _Alloc>::forward_list(const allocator_type& __a)
    : base(__a)
{
}

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(size_type __n)
{
    if (__n > 0)
    {
        __node_allocator& __a = base::__alloc();
        typedef __allocator_destructor<__node_allocator> _Dp;
        unique_ptr<__node, _Dp> __h(nullptr, _Dp(__a, 1));
        for (__begin_node_pointer __p = base::__before_begin(); __n > 0; --__n,
                                                             __p = __p->__next_as_begin())
        {
            __h.reset(__node_traits::allocate(__a, 1));
            __node_traits::construct(__a, _VSTD::addressof(__h->__value_));
            __h->__next_ = nullptr;
            __p->__next_ = __h.release();
        }
    }
}

#if _LIBCPP_STD_VER > 11
template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(size_type __n,
                                        const allocator_type& __base_alloc)
    : base ( __base_alloc )
{
    if (__n > 0)
    {
        __node_allocator& __a = base::__alloc();
        typedef __allocator_destructor<__node_allocator> _Dp;
        unique_ptr<__node, _Dp> __h(nullptr, _Dp(__a, 1));
        for (__begin_node_pointer __p = base::__before_begin(); __n > 0; --__n,
                                                             __p = __p->__next_as_begin())
        {
            __h.reset(__node_traits::allocate(__a, 1));
            __node_traits::construct(__a, _VSTD::addressof(__h->__value_));
            __h->__next_ = nullptr;
            __p->__next_ = __h.release();
        }
    }
}
#endif

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(size_type __n, const value_type& __v)
{
    insert_after(cbefore_begin(), __n, __v);
}

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(size_type __n, const value_type& __v,
                                        const allocator_type& __a)
    : base(__a)
{
    insert_after(cbefore_begin(), __n, __v);
}

template <class _Tp, class _Alloc>
template <class _InputIterator>
forward_list<_Tp, _Alloc>::forward_list(_InputIterator __f, _InputIterator __l,
                                        typename enable_if<
                                          __is_input_iterator<_InputIterator>::value
                                        >::type*)
{
    insert_after(cbefore_begin(), __f, __l);
}

template <class _Tp, class _Alloc>
template <class _InputIterator>
forward_list<_Tp, _Alloc>::forward_list(_InputIterator __f, _InputIterator __l,
                                        const allocator_type& __a,
                                        typename enable_if<
                                          __is_input_iterator<_InputIterator>::value
                                        >::type*)
    : base(__a)
{
    insert_after(cbefore_begin(), __f, __l);
}

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(const forward_list& __x)
    : base(allocator_type(
             __node_traits::select_on_container_copy_construction(__x.__alloc())
                         )
          )
{
    insert_after(cbefore_begin(), __x.begin(), __x.end());
}

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(const forward_list& __x,
                                        const allocator_type& __a)
    : base(__a)
{
    insert_after(cbefore_begin(), __x.begin(), __x.end());
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(forward_list&& __x,
                                        const allocator_type& __a)
    : base(_VSTD::move(__x), __a)
{
    if (base::__alloc() != __x.__alloc())
    {
        typedef move_iterator<iterator> _Ip;
        insert_after(cbefore_begin(), _Ip(__x.begin()), _Ip(__x.end()));
    }
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(initializer_list<value_type> __il)
{
    insert_after(cbefore_begin(), __il.begin(), __il.end());
}

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>::forward_list(initializer_list<value_type> __il,
                                        const allocator_type& __a)
    : base(__a)
{
    insert_after(cbefore_begin(), __il.begin(), __il.end());
}

#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

template <class _Tp, class _Alloc>
forward_list<_Tp, _Alloc>&
forward_list<_Tp, _Alloc>::operator=(const forward_list& __x)
{
    if (this != &__x)
    {
        base::__copy_assign_alloc(__x);
        assign(__x.begin(), __x.end());
    }
    return *this;
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::__move_assign(forward_list& __x, true_type)
    _NOEXCEPT_(is_nothrow_move_assignable<allocator_type>::value)
{
    clear();
    base::__move_assign_alloc(__x);
    base::__before_begin()->__next_ = __x.__before_begin()->__next_;
    __x.__before_begin()->__next_ = nullptr;
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::__move_assign(forward_list& __x, false_type)
{
    if (base::__alloc() == __x.__alloc())
        __move_assign(__x, true_type());
    else
    {
        typedef move_iterator<iterator> _Ip;
        assign(_Ip(__x.begin()), _Ip(__x.end()));
    }
}

template <class _Tp, class _Alloc>
inline
forward_list<_Tp, _Alloc>&
forward_list<_Tp, _Alloc>::operator=(forward_list&& __x)
    _NOEXCEPT_(
             __node_traits::propagate_on_container_move_assignment::value &&
             is_nothrow_move_assignable<allocator_type>::value)
{
    __move_assign(__x, integral_constant<bool,
          __node_traits::propagate_on_container_move_assignment::value>());
    return *this;
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

template <class _Tp, class _Alloc>
inline
forward_list<_Tp, _Alloc>&
forward_list<_Tp, _Alloc>::operator=(initializer_list<value_type> __il)
{
    assign(__il.begin(), __il.end());
    return *this;
}

#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

template <class _Tp, class _Alloc>
template <class _InputIterator>
typename enable_if
<
    __is_input_iterator<_InputIterator>::value,
    void
>::type
forward_list<_Tp, _Alloc>::assign(_InputIterator __f, _InputIterator __l)
{
    iterator __i = before_begin();
    iterator __j = _VSTD::next(__i);
    iterator __e = end();
    for (; __j != __e && __f != __l; ++__i, (void) ++__j, ++__f)
        *__j = *__f;
    if (__j == __e)
        insert_after(__i, __f, __l);
    else
        erase_after(__i, __e);
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::assign(size_type __n, const value_type& __v)
{
    iterator __i = before_begin();
    iterator __j = _VSTD::next(__i);
    iterator __e = end();
    for (; __j != __e && __n > 0; --__n, ++__i, ++__j)
        *__j = __v;
    if (__j == __e)
        insert_after(__i, __n, __v);
    else
        erase_after(__i, __e);
}

#ifndef _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

template <class _Tp, class _Alloc>
inline
void
forward_list<_Tp, _Alloc>::assign(initializer_list<value_type> __il)
{
    assign(__il.begin(), __il.end());
}

#endif  // _LIBCPP_HAS_NO_GENERALIZED_INITIALIZERS

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
#ifndef _LIBCPP_HAS_NO_VARIADICS

template <class _Tp, class _Alloc>
template <class... _Args>
#if _LIBCPP_STD_VER > 14
typename forward_list<_Tp, _Alloc>::reference
#else
void
#endif
forward_list<_Tp, _Alloc>::emplace_front(_Args&&... __args)
{
    __node_allocator& __a = base::__alloc();
    typedef __allocator_destructor<__node_allocator> _Dp;
    unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
    __node_traits::construct(__a, _VSTD::addressof(__h->__value_),
                                  _VSTD::forward<_Args>(__args)...);
    __h->__next_ = base::__before_begin()->__next_;
    base::__before_begin()->__next_ = __h.release();
#if _LIBCPP_STD_VER > 14
    return base::__before_begin()->__next_->__value_;
#endif
}

#endif  // _LIBCPP_HAS_NO_VARIADICS

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::push_front(value_type&& __v)
{
    __node_allocator& __a = base::__alloc();
    typedef __allocator_destructor<__node_allocator> _Dp;
    unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
    __node_traits::construct(__a, _VSTD::addressof(__h->__value_), _VSTD::move(__v));
    __h->__next_ = base::__before_begin()->__next_;
    base::__before_begin()->__next_ = __h.release();
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::push_front(const value_type& __v)
{
    __node_allocator& __a = base::__alloc();
    typedef __allocator_destructor<__node_allocator> _Dp;
    unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
    __node_traits::construct(__a, _VSTD::addressof(__h->__value_), __v);
    __h->__next_ = base::__before_begin()->__next_;
    base::__before_begin()->__next_ = __h.release();
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::pop_front()
{
    __node_allocator& __a = base::__alloc();
    __node_pointer __p = base::__before_begin()->__next_;
    base::__before_begin()->__next_ = __p->__next_;
    __node_traits::destroy(__a, _VSTD::addressof(__p->__value_));
    __node_traits::deallocate(__a, __p, 1);
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
#ifndef _LIBCPP_HAS_NO_VARIADICS

template <class _Tp, class _Alloc>
template <class... _Args>
typename forward_list<_Tp, _Alloc>::iterator
forward_list<_Tp, _Alloc>::emplace_after(const_iterator __p, _Args&&... __args)
{
    __begin_node_pointer const __r = __p.__get_begin();
    __node_allocator& __a = base::__alloc();
    typedef __allocator_destructor<__node_allocator> _Dp;
    unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
    __node_traits::construct(__a, _VSTD::addressof(__h->__value_),
                                  _VSTD::forward<_Args>(__args)...);
    __h->__next_ = __r->__next_;
    __r->__next_ = __h.release();
    return iterator(__r->__next_);
}

#endif  // _LIBCPP_HAS_NO_VARIADICS

template <class _Tp, class _Alloc>
typename forward_list<_Tp, _Alloc>::iterator
forward_list<_Tp, _Alloc>::insert_after(const_iterator __p, value_type&& __v)
{
    __begin_node_pointer const __r = __p.__get_begin();
    __node_allocator& __a = base::__alloc();
    typedef __allocator_destructor<__node_allocator> _Dp;
    unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
    __node_traits::construct(__a, _VSTD::addressof(__h->__value_), _VSTD::move(__v));
    __h->__next_ = __r->__next_;
    __r->__next_ = __h.release();
    return iterator(__r->__next_);
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
typename forward_list<_Tp, _Alloc>::iterator
forward_list<_Tp, _Alloc>::insert_after(const_iterator __p, const value_type& __v)
{
    __begin_node_pointer const __r = __p.__get_begin();
    __node_allocator& __a = base::__alloc();
    typedef __allocator_destructor<__node_allocator> _Dp;
    unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
    __node_traits::construct(__a, _VSTD::addressof(__h->__value_), __v);
    __h->__next_ = __r->__next_;
    __r->__next_ = __h.release();
    return iterator(__r->__next_);
}

template <class _Tp, class _Alloc>
typename forward_list<_Tp, _Alloc>::iterator
forward_list<_Tp, _Alloc>::insert_after(const_iterator __p, size_type __n,
                                        const value_type& __v)
{
    __begin_node_pointer __r = __p.__get_begin();
    if (__n > 0)
    {
        __node_allocator& __a = base::__alloc();
        typedef __allocator_destructor<__node_allocator> _Dp;
        unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
        __node_traits::construct(__a, _VSTD::addressof(__h->__value_), __v);
        __node_pointer __first = __h.release();
        __node_pointer __last = __first;
#ifndef _LIBCPP_NO_EXCEPTIONS
        try
        {
#endif  // _LIBCPP_NO_EXCEPTIONS
            for (--__n; __n != 0; --__n, __last = __last->__next_)
            {
                __h.reset(__node_traits::allocate(__a, 1));
                __node_traits::construct(__a, _VSTD::addressof(__h->__value_), __v);
                __last->__next_ = __h.release();
            }
#ifndef _LIBCPP_NO_EXCEPTIONS
        }
        catch (...)
        {
            while (__first != nullptr)
            {
                __node_pointer __next = __first->__next_;
                __node_traits::destroy(__a, _VSTD::addressof(__first->__value_));
                __node_traits::deallocate(__a, __first, 1);
                __first = __next;
            }
            throw;
        }
#endif  // _LIBCPP_NO_EXCEPTIONS
        __last->__next_ = __r->__next_;
        __r->__next_ = __first;
        __r = static_cast<__begin_node_pointer>(__last);
    }
    return iterator(__r);
}

template <class _Tp, class _Alloc>
template <class _InputIterator>
typename enable_if
<
    __is_input_iterator<_InputIterator>::value,
    typename forward_list<_Tp, _Alloc>::iterator
>::type
forward_list<_Tp, _Alloc>::insert_after(const_iterator __p,
                                        _InputIterator __f, _InputIterator __l)
{
    __begin_node_pointer __r = __p.__get_begin();
    if (__f != __l)
    {
        __node_allocator& __a = base::__alloc();
        typedef __allocator_destructor<__node_allocator> _Dp;
        unique_ptr<__node, _Dp> __h(__node_traits::allocate(__a, 1), _Dp(__a, 1));
        __node_traits::construct(__a, _VSTD::addressof(__h->__value_), *__f);
        __node_pointer __first = __h.release();
        __node_pointer __last = __first;
#ifndef _LIBCPP_NO_EXCEPTIONS
        try
        {
#endif  // _LIBCPP_NO_EXCEPTIONS
            for (++__f; __f != __l; ++__f, ((void)(__last = __last->__next_)))
            {
                __h.reset(__node_traits::allocate(__a, 1));
                __node_traits::construct(__a, _VSTD::addressof(__h->__value_), *__f);
                __last->__next_ = __h.release();
            }
#ifndef _LIBCPP_NO_EXCEPTIONS
        }
        catch (...)
        {
            while (__first != nullptr)
            {
                __node_pointer __next = __first->__next_;
                __node_traits::destroy(__a, _VSTD::addressof(__first->__value_));
                __node_traits::deallocate(__a, __first, 1);
                __first = __next;
            }
            throw;
        }
#endif  // _LIBCPP_NO_EXCEPTIONS
        __last->__next_ = __r->__next_;
        __r->__next_ = __first;
        __r = static_cast<__begin_node_pointer>(__last);
    }
    return iterator(__r);
}

template <class _Tp, class _Alloc>
typename forward_list<_Tp, _Alloc>::iterator
forward_list<_Tp, _Alloc>::erase_after(const_iterator __f)
{
    __begin_node_pointer __p = __f.__get_begin();
    __node_pointer __n = __p->__next_;
    __p->__next_ = __n->__next_;
    __node_allocator& __a = base::__alloc();
    __node_traits::destroy(__a, _VSTD::addressof(__n->__value_));
    __node_traits::deallocate(__a, __n, 1);
    return iterator(__p->__next_);
}

template <class _Tp, class _Alloc>
typename forward_list<_Tp, _Alloc>::iterator
forward_list<_Tp, _Alloc>::erase_after(const_iterator __f, const_iterator __l)
{
    __node_pointer __e = __l.__get_unsafe_node_pointer();
    if (__f != __l)
    {
        __begin_node_pointer __bp = __f.__get_begin();

        __node_pointer __n = __bp->__next_;
        if (__n != __e)
        {
            __bp->__next_ = __e;
            __node_allocator& __a = base::__alloc();
            do
            {
                __node_pointer __tmp = __n->__next_;
                __node_traits::destroy(__a, _VSTD::addressof(__n->__value_));
                __node_traits::deallocate(__a, __n, 1);
                __n = __tmp;
            } while (__n != __e);
        }
    }
    return iterator(__e);
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::resize(size_type __n)
{
    size_type __sz = 0;
    iterator __p = before_begin();
    iterator __i = begin();
    iterator __e = end();
    for (; __i != __e && __sz < __n; ++__p, ++__i, ++__sz)
        ;
    if (__i != __e)
        erase_after(__p, __e);
    else
    {
        __n -= __sz;
        if (__n > 0)
        {
            __node_allocator& __a = base::__alloc();
            typedef __allocator_destructor<__node_allocator> _Dp;
            unique_ptr<__node, _Dp> __h(nullptr, _Dp(__a, 1));
            for (__begin_node_pointer __ptr = __p.__get_begin(); __n > 0; --__n,
                                                         __ptr = __ptr->__next_as_begin())
            {
                __h.reset(__node_traits::allocate(__a, 1));
                __node_traits::construct(__a, _VSTD::addressof(__h->__value_));
                __h->__next_ = nullptr;
                __ptr->__next_ = __h.release();
            }
        }
    }
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::resize(size_type __n, const value_type& __v)
{
    size_type __sz = 0;
    iterator __p = before_begin();
    iterator __i = begin();
    iterator __e = end();
    for (; __i != __e && __sz < __n; ++__p, ++__i, ++__sz)
        ;
    if (__i != __e)
        erase_after(__p, __e);
    else
    {
        __n -= __sz;
        if (__n > 0)
        {
            __node_allocator& __a = base::__alloc();
            typedef __allocator_destructor<__node_allocator> _Dp;
            unique_ptr<__node, _Dp> __h(nullptr, _Dp(__a, 1));
            for (__begin_node_pointer __ptr = __p.__get_begin(); __n > 0; --__n,
                                                         __ptr = __ptr->__next_as_begin())
            {
                __h.reset(__node_traits::allocate(__a, 1));
                __node_traits::construct(__a, _VSTD::addressof(__h->__value_), __v);
                __h->__next_ = nullptr;
                __ptr->__next_ = __h.release();
            }
        }
    }
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::splice_after(const_iterator __p,
                                        forward_list& __x)
{
    if (!__x.empty())
    {
        if (__p.__get_begin()->__next_ != nullptr)
        {
            const_iterator __lm1 = __x.before_begin();
            while (__lm1.__get_begin()->__next_ != nullptr)
                ++__lm1;
            __lm1.__get_begin()->__next_ = __p.__get_begin()->__next_;
        }
        __p.__get_begin()->__next_ = __x.__before_begin()->__next_;
        __x.__before_begin()->__next_ = nullptr;
    }
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::splice_after(const_iterator __p,
                                        forward_list& /*__other*/,
                                        const_iterator __i)
{
    const_iterator __lm1 = _VSTD::next(__i);
    if (__p != __i && __p != __lm1)
    {
        __i.__get_begin()->__next_ = __lm1.__get_begin()->__next_;
        __lm1.__get_begin()->__next_ = __p.__get_begin()->__next_;
        __p.__get_begin()->__next_ = __lm1.__get_unsafe_node_pointer();
    }
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::splice_after(const_iterator __p,
                                        forward_list& /*__other*/,
                                        const_iterator __f, const_iterator __l)
{
    if (__f != __l && __p != __f)
    {
        const_iterator __lm1 = __f;
        while (__lm1.__get_begin()->__next_ != __l.__get_begin())
            ++__lm1;
        if (__f != __lm1)
        {
            __lm1.__get_begin()->__next_ = __p.__get_begin()->__next_;
            __p.__get_begin()->__next_ = __f.__get_begin()->__next_;
            __f.__get_begin()->__next_ = __l.__get_unsafe_node_pointer();
        }
    }
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
void
forward_list<_Tp, _Alloc>::splice_after(const_iterator __p,
                                        forward_list&& __x)
{
    splice_after(__p, __x);
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
void
forward_list<_Tp, _Alloc>::splice_after(const_iterator __p,
                                        forward_list&& __x,
                                        const_iterator __i)
{
    splice_after(__p, __x, __i);
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
void
forward_list<_Tp, _Alloc>::splice_after(const_iterator __p,
                                        forward_list&& __x,
                                        const_iterator __f, const_iterator __l)
{
    splice_after(__p, __x, __f, __l);
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::remove(const value_type& __v)
{
    forward_list<_Tp, _Alloc> __deleted_nodes; // collect the nodes we're removing
    iterator __e = end();
    for (iterator __i = before_begin(); __i.__get_begin()->__next_ != nullptr;)
    {
        if (__i.__get_begin()->__next_->__value_ == __v)
        {
            iterator __j = _VSTD::next(__i, 2);
            for (; __j != __e && *__j == __v; ++__j)
                ;
            __deleted_nodes.splice_after(__deleted_nodes.before_begin(), *this, __i, __j);
            if (__j == __e)
                break;
            __i = __j;
        }
        else
            ++__i;
    }
}

template <class _Tp, class _Alloc>
template <class _Predicate>
void
forward_list<_Tp, _Alloc>::remove_if(_Predicate __pred)
{
    iterator __e = end();
    for (iterator __i = before_begin(); __i.__get_begin()->__next_ != nullptr;)
    {
        if (__pred(__i.__get_begin()->__next_->__value_))
        {
            iterator __j = _VSTD::next(__i, 2);
            for (; __j != __e && __pred(*__j); ++__j)
                ;
            erase_after(__i, __j);
            if (__j == __e)
                break;
            __i = __j;
        }
        else
            ++__i;
    }
}

template <class _Tp, class _Alloc>
template <class _BinaryPredicate>
void
forward_list<_Tp, _Alloc>::unique(_BinaryPredicate __binary_pred)
{
    for (iterator __i = begin(), __e = end(); __i != __e;)
    {
        iterator __j = _VSTD::next(__i);
        for (; __j != __e && __binary_pred(*__i, *__j); ++__j)
            ;
        if (__i.__get_begin()->__next_ != __j.__get_unsafe_node_pointer())
            erase_after(__i, __j);
        __i = __j;
    }
}

template <class _Tp, class _Alloc>
template <class _Compare>
void
forward_list<_Tp, _Alloc>::merge(forward_list& __x, _Compare __comp)
{
    if (this != &__x)
    {
        base::__before_begin()->__next_ = __merge(base::__before_begin()->__next_,
                                                    __x.__before_begin()->__next_,
                                                    __comp);
        __x.__before_begin()->__next_ = nullptr;
    }
}

template <class _Tp, class _Alloc>
template <class _Compare>
typename forward_list<_Tp, _Alloc>::__node_pointer
forward_list<_Tp, _Alloc>::__merge(__node_pointer __f1, __node_pointer __f2,
                                   _Compare& __comp)
{
    if (__f1 == nullptr)
        return __f2;
    if (__f2 == nullptr)
        return __f1;
    __node_pointer __r;
    if (__comp(__f2->__value_, __f1->__value_))
    {
        __node_pointer __t = __f2;
        while (__t->__next_ != nullptr &&
                             __comp(__t->__next_->__value_, __f1->__value_))
            __t = __t->__next_;
        __r = __f2;
        __f2 = __t->__next_;
        __t->__next_ = __f1;
    }
    else
        __r = __f1;
    __node_pointer __p = __f1;
    __f1 = __f1->__next_;
    while (__f1 != nullptr && __f2 != nullptr)
    {
        if (__comp(__f2->__value_, __f1->__value_))
        {
            __node_pointer __t = __f2;
            while (__t->__next_ != nullptr &&
                                 __comp(__t->__next_->__value_, __f1->__value_))
                __t = __t->__next_;
            __p->__next_ = __f2;
            __f2 = __t->__next_;
            __t->__next_ = __f1;
        }
        __p = __f1;
        __f1 = __f1->__next_;
    }
    if (__f2 != nullptr)
        __p->__next_ = __f2;
    return __r;
}

template <class _Tp, class _Alloc>
template <class _Compare>
inline
void
forward_list<_Tp, _Alloc>::sort(_Compare __comp)
{
    base::__before_begin()->__next_ = __sort(base::__before_begin()->__next_,
                                       _VSTD::distance(begin(), end()), __comp);
}

template <class _Tp, class _Alloc>
template <class _Compare>
typename forward_list<_Tp, _Alloc>::__node_pointer
forward_list<_Tp, _Alloc>::__sort(__node_pointer __f1, difference_type __sz,
                                  _Compare& __comp)
{
    switch (__sz)
    {
    case 0:
    case 1:
        return __f1;
    case 2:
        if (__comp(__f1->__next_->__value_, __f1->__value_))
        {
            __node_pointer __t = __f1->__next_;
            __t->__next_ = __f1;
            __f1->__next_ = nullptr;
            __f1 = __t;
        }
        return __f1;
    }
    difference_type __sz1 = __sz / 2;
    difference_type __sz2 = __sz - __sz1;
    __node_pointer __t = _VSTD::next(iterator(__f1), __sz1 - 1).__get_unsafe_node_pointer();
    __node_pointer __f2 = __t->__next_;
    __t->__next_ = nullptr;
    return __merge(__sort(__f1, __sz1, __comp),
                   __sort(__f2, __sz2, __comp), __comp);
}

template <class _Tp, class _Alloc>
void
forward_list<_Tp, _Alloc>::reverse() _NOEXCEPT
{
    __node_pointer __p = base::__before_begin()->__next_;
    if (__p != nullptr)
    {
        __node_pointer __f = __p->__next_;
        __p->__next_ = nullptr;
        while (__f != nullptr)
        {
            __node_pointer __t = __f->__next_;
            __f->__next_ = __p;
            __p = __f;
            __f = __t;
        }
        base::__before_begin()->__next_ = __p;
    }
}

template <class _Tp, class _Alloc>
bool operator==(const forward_list<_Tp, _Alloc>& __x,
                const forward_list<_Tp, _Alloc>& __y)
{
    typedef forward_list<_Tp, _Alloc> _Cp;
    typedef typename _Cp::const_iterator _Ip;
    _Ip __ix = __x.begin();
    _Ip __ex = __x.end();
    _Ip __iy = __y.begin();
    _Ip __ey = __y.end();
    for (; __ix != __ex && __iy != __ey; ++__ix, ++__iy)
        if (!(*__ix == *__iy))
            return false;
    return (__ix == __ex) == (__iy == __ey);
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
bool operator!=(const forward_list<_Tp, _Alloc>& __x,
                const forward_list<_Tp, _Alloc>& __y)
{
    return !(__x == __y);
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
bool operator< (const forward_list<_Tp, _Alloc>& __x,
                const forward_list<_Tp, _Alloc>& __y)
{
    return _VSTD::lexicographical_compare(__x.begin(), __x.end(),
                                         __y.begin(), __y.end());
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
bool operator> (const forward_list<_Tp, _Alloc>& __x,
                const forward_list<_Tp, _Alloc>& __y)
{
    return __y < __x;
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
bool operator>=(const forward_list<_Tp, _Alloc>& __x,
                const forward_list<_Tp, _Alloc>& __y)
{
    return !(__x < __y);
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
bool operator<=(const forward_list<_Tp, _Alloc>& __x,
                const forward_list<_Tp, _Alloc>& __y)
{
    return !(__y < __x);
}

template <class _Tp, class _Alloc>
inline _LIBCPP_INLINE_VISIBILITY
void
swap(forward_list<_Tp, _Alloc>& __x, forward_list<_Tp, _Alloc>& __y)
    _NOEXCEPT_(_NOEXCEPT_(__x.swap(__y)))
{
    __x.swap(__y);
}

_LIBCPP_END_NAMESPACE_STD

#endif  // _LIBCPP_FORWARD_LIST
```

## Официальная реализация контейнера Stack

```
// -*- C++ -*-
//===---------------------------- stack -----------------------------------===//
//
//                     The LLVM Compiler Infrastructure
//
// This file is dual licensed under the MIT and the University of Illinois Open
// Source Licenses. See LICENSE.TXT for details.
//
//===----------------------------------------------------------------------===//

#ifndef _LIBCPP_STACK
#define _LIBCPP_STACK

/*
    stack synopsis

namespace std
{

template <class T, class Container = deque<T>>
class stack
{
public:
    typedef Container                                container_type;
    typedef typename container_type::value_type      value_type;
    typedef typename container_type::reference       reference;
    typedef typename container_type::const_reference const_reference;
    typedef typename container_type::size_type       size_type;

protected:
    container_type c;

public:
    stack() = default;
    ~stack() = default;

    stack(const stack& q) = default;
    stack(stack&& q) = default;

    stack& operator=(const stack& q) = default;
    stack& operator=(stack&& q) = default;

    explicit stack(const container_type& c);
    explicit stack(container_type&& c);
    template <class Alloc> explicit stack(const Alloc& a);
    template <class Alloc> stack(const container_type& c, const Alloc& a);
    template <class Alloc> stack(container_type&& c, const Alloc& a);
    template <class Alloc> stack(const stack& c, const Alloc& a);
    template <class Alloc> stack(stack&& c, const Alloc& a);

    bool empty() const;
    size_type size() const;
    reference top();
    const_reference top() const;

    void push(const value_type& x);
    void push(value_type&& x);
    template <class... Args> reference emplace(Args&&... args); // reference in C++17
    void pop();

    void swap(stack& c) noexcept(is_nothrow_swappable_v<Container>)
};

template <class T, class Container>
  bool operator==(const stack<T, Container>& x, const stack<T, Container>& y);
template <class T, class Container>
  bool operator< (const stack<T, Container>& x, const stack<T, Container>& y);
template <class T, class Container>
  bool operator!=(const stack<T, Container>& x, const stack<T, Container>& y);
template <class T, class Container>
  bool operator> (const stack<T, Container>& x, const stack<T, Container>& y);
template <class T, class Container>
  bool operator>=(const stack<T, Container>& x, const stack<T, Container>& y);
template <class T, class Container>
  bool operator<=(const stack<T, Container>& x, const stack<T, Container>& y);

template <class T, class Container>
  void swap(stack<T, Container>& x, stack<T, Container>& y)
  noexcept(noexcept(x.swap(y)));

}  // std

*/

#include <__config>
#include <deque>

#if !defined(_LIBCPP_HAS_NO_PRAGMA_SYSTEM_HEADER)
#pragma GCC system_header
#endif

_LIBCPP_BEGIN_NAMESPACE_STD

template <class _Tp, class _Container = deque<_Tp> > class _LIBCPP_TEMPLATE_VIS stack;

template <class _Tp, class _Container>
_LIBCPP_INLINE_VISIBILITY
bool
operator==(const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y);

template <class _Tp, class _Container>
_LIBCPP_INLINE_VISIBILITY
bool
operator< (const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y);

template <class _Tp, class _Container /*= deque<_Tp>*/>
class _LIBCPP_TEMPLATE_VIS stack
{
public:
    typedef _Container                               container_type;
    typedef typename container_type::value_type      value_type;
    typedef typename container_type::reference       reference;
    typedef typename container_type::const_reference const_reference;
    typedef typename container_type::size_type       size_type;
    static_assert((is_same<_Tp, value_type>::value), "" );
    
protected:
    container_type c;

public:
    _LIBCPP_INLINE_VISIBILITY
    stack()
        _NOEXCEPT_(is_nothrow_default_constructible<container_type>::value)
        : c() {}

    _LIBCPP_INLINE_VISIBILITY
    stack(const stack& __q) : c(__q.c) {}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    stack(stack&& __q)
        _NOEXCEPT_(is_nothrow_move_constructible<container_type>::value)
        : c(_VSTD::move(__q.c)) {}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    stack& operator=(const stack& __q) {c = __q.c; return *this;}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    stack& operator=(stack&& __q)
        _NOEXCEPT_(is_nothrow_move_assignable<container_type>::value)
        {c = _VSTD::move(__q.c); return *this;}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    explicit stack(const container_type& __c) : c(__c) {}
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    explicit stack(container_type&& __c) : c(_VSTD::move(__c)) {}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        explicit stack(const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0)
            : c(__a) {}
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        stack(const container_type& __c, const _Alloc& __a,
              typename enable_if<uses_allocator<container_type,
                                                _Alloc>::value>::type* = 0)
            : c(__c, __a) {}
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        stack(const stack& __s, const _Alloc& __a,
              typename enable_if<uses_allocator<container_type,
                                                _Alloc>::value>::type* = 0)
            : c(__s.c, __a) {}
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        stack(container_type&& __c, const _Alloc& __a,
              typename enable_if<uses_allocator<container_type,
                                                _Alloc>::value>::type* = 0)
            : c(_VSTD::move(__c), __a) {}
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        stack(stack&& __s, const _Alloc& __a,
              typename enable_if<uses_allocator<container_type,
                                                _Alloc>::value>::type* = 0)
            : c(_VSTD::move(__s.c), __a) {}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    bool empty()     const      {return c.empty();}
    _LIBCPP_INLINE_VISIBILITY
    size_type size() const      {return c.size();}
    _LIBCPP_INLINE_VISIBILITY
    reference top()             {return c.back();}
    _LIBCPP_INLINE_VISIBILITY
    const_reference top() const {return c.back();}

    _LIBCPP_INLINE_VISIBILITY
    void push(const value_type& __v) {c.push_back(__v);}
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void push(value_type&& __v) {c.push_back(_VSTD::move(__v));}
#ifndef _LIBCPP_HAS_NO_VARIADICS
    template <class... _Args>
        _LIBCPP_INLINE_VISIBILITY
#if _LIBCPP_STD_VER > 14
        reference emplace(_Args&&... __args)
        { return c.emplace_back(_VSTD::forward<_Args>(__args)...);}
#else
        void      emplace(_Args&&... __args)
        {        c.emplace_back(_VSTD::forward<_Args>(__args)...);}
#endif
#endif  // _LIBCPP_HAS_NO_VARIADICS
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void pop() {c.pop_back();}

    _LIBCPP_INLINE_VISIBILITY
    void swap(stack& __s)
        _NOEXCEPT_(__is_nothrow_swappable<container_type>::value)
    {
        using _VSTD::swap;
        swap(c, __s.c);
    }

    template <class T1, class _C1>
    friend
    bool
    operator==(const stack<T1, _C1>& __x, const stack<T1, _C1>& __y);

    template <class T1, class _C1>
    friend
    bool
    operator< (const stack<T1, _C1>& __x, const stack<T1, _C1>& __y);
};

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator==(const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y)
{
    return __x.c == __y.c;
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator< (const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y)
{
    return __x.c < __y.c;
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator!=(const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y)
{
    return !(__x == __y);
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator> (const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y)
{
    return __y < __x;
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator>=(const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y)
{
    return !(__x < __y);
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator<=(const stack<_Tp, _Container>& __x, const stack<_Tp, _Container>& __y)
{
    return !(__y < __x);
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
typename enable_if<
    __is_swappable<_Container>::value,
    void
>::type
swap(stack<_Tp, _Container>& __x, stack<_Tp, _Container>& __y)
    _NOEXCEPT_(_NOEXCEPT_(__x.swap(__y)))
{
    __x.swap(__y);
}

template <class _Tp, class _Container, class _Alloc>
struct _LIBCPP_TEMPLATE_VIS uses_allocator<stack<_Tp, _Container>, _Alloc>
    : public uses_allocator<_Container, _Alloc>
{
};

_LIBCPP_END_NAMESPACE_STD

#endif  // _LIBCPP_STACK
```

## Официальная реализация контейнера Queue

```
// -*- C++ -*-
//===--------------------------- queue ------------------------------------===//
//
//                     The LLVM Compiler Infrastructure
//
// This file is dual licensed under the MIT and the University of Illinois Open
// Source Licenses. See LICENSE.TXT for details.
//
//===----------------------------------------------------------------------===//

#ifndef _LIBCPP_QUEUE
#define _LIBCPP_QUEUE

/*
    queue synopsis

namespace std
{

template <class T, class Container = deque<T>>
class queue
{
public:
    typedef Container                                container_type;
    typedef typename container_type::value_type      value_type;
    typedef typename container_type::reference       reference;
    typedef typename container_type::const_reference const_reference;
    typedef typename container_type::size_type       size_type;

protected:
    container_type c;

public:
    queue() = default;
    ~queue() = default;

    queue(const queue& q) = default;
    queue(queue&& q) = default;

    queue& operator=(const queue& q) = default;
    queue& operator=(queue&& q) = default;

    explicit queue(const container_type& c);
    explicit queue(container_type&& c)
    template <class Alloc>
        explicit queue(const Alloc& a);
    template <class Alloc>
        queue(const container_type& c, const Alloc& a);
    template <class Alloc>
        queue(container_type&& c, const Alloc& a);
    template <class Alloc>
        queue(const queue& q, const Alloc& a);
    template <class Alloc>
        queue(queue&& q, const Alloc& a);

    bool      empty() const;
    size_type size() const;

    reference       front();
    const_reference front() const;
    reference       back();
    const_reference back() const;

    void push(const value_type& v);
    void push(value_type&& v);
    template <class... Args> reference emplace(Args&&... args); // reference in C++17
    void pop();

    void swap(queue& q) noexcept(is_nothrow_swappable_v<Container>)
};

template <class T, class Container>
  bool operator==(const queue<T, Container>& x,const queue<T, Container>& y);

template <class T, class Container>
  bool operator< (const queue<T, Container>& x,const queue<T, Container>& y);

template <class T, class Container>
  bool operator!=(const queue<T, Container>& x,const queue<T, Container>& y);

template <class T, class Container>
  bool operator> (const queue<T, Container>& x,const queue<T, Container>& y);

template <class T, class Container>
  bool operator>=(const queue<T, Container>& x,const queue<T, Container>& y);

template <class T, class Container>
  bool operator<=(const queue<T, Container>& x,const queue<T, Container>& y);

template <class T, class Container>
  void swap(queue<T, Container>& x, queue<T, Container>& y)
  noexcept(noexcept(x.swap(y)));

template <class T, class Container = vector<T>,
          class Compare = less<typename Container::value_type>>
class priority_queue
{
public:
    typedef Container                                container_type;
    typedef typename container_type::value_type      value_type;
    typedef typename container_type::reference       reference;
    typedef typename container_type::const_reference const_reference;
    typedef typename container_type::size_type       size_type;

protected:
    container_type c;
    Compare comp;

public:
    priority_queue() = default;
    ~priority_queue() = default;

    priority_queue(const priority_queue& q) = default;
    priority_queue(priority_queue&& q) = default;

    priority_queue& operator=(const priority_queue& q) = default;
    priority_queue& operator=(priority_queue&& q) = default;

    explicit priority_queue(const Compare& comp);
    priority_queue(const Compare& comp, const container_type& c);
    explicit priority_queue(const Compare& comp, container_type&& c);
    template <class InputIterator>
        priority_queue(InputIterator first, InputIterator last,
                       const Compare& comp = Compare());
    template <class InputIterator>
        priority_queue(InputIterator first, InputIterator last,
                       const Compare& comp, const container_type& c);
    template <class InputIterator>
        priority_queue(InputIterator first, InputIterator last,
                       const Compare& comp, container_type&& c);
    template <class Alloc>
        explicit priority_queue(const Alloc& a);
    template <class Alloc>
        priority_queue(const Compare& comp, const Alloc& a);
    template <class Alloc>
        priority_queue(const Compare& comp, const container_type& c,
                       const Alloc& a);
    template <class Alloc>
        priority_queue(const Compare& comp, container_type&& c,
                       const Alloc& a);
    template <class Alloc>
        priority_queue(const priority_queue& q, const Alloc& a);
    template <class Alloc>
        priority_queue(priority_queue&& q, const Alloc& a);

    bool            empty() const;
    size_type       size() const;
    const_reference top() const;

    void push(const value_type& v);
    void push(value_type&& v);
    template <class... Args> void emplace(Args&&... args);
    void pop();

    void swap(priority_queue& q)
        noexcept(is_nothrow_swappable_v<Container> &&
                 is_nothrow_swappable_v<Comp>)
};

template <class T, class Container, class Compare>
  void swap(priority_queue<T, Container, Compare>& x,
            priority_queue<T, Container, Compare>& y)
            noexcept(noexcept(x.swap(y)));

}  // std

*/

#include <__config>
#include <deque>
#include <vector>
#include <functional>
#include <algorithm>

#if !defined(_LIBCPP_HAS_NO_PRAGMA_SYSTEM_HEADER)
#pragma GCC system_header
#endif

_LIBCPP_BEGIN_NAMESPACE_STD

template <class _Tp, class _Container = deque<_Tp> > class _LIBCPP_TEMPLATE_VIS queue;

template <class _Tp, class _Container>
_LIBCPP_INLINE_VISIBILITY
bool
operator==(const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y);

template <class _Tp, class _Container>
_LIBCPP_INLINE_VISIBILITY
bool
operator< (const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y);

template <class _Tp, class _Container /*= deque<_Tp>*/>
class _LIBCPP_TEMPLATE_VIS queue
{
public:
    typedef _Container                               container_type;
    typedef typename container_type::value_type      value_type;
    typedef typename container_type::reference       reference;
    typedef typename container_type::const_reference const_reference;
    typedef typename container_type::size_type       size_type;
    static_assert((is_same<_Tp, value_type>::value), "" );

protected:
    container_type c;

public:
    _LIBCPP_INLINE_VISIBILITY
    queue()
        _NOEXCEPT_(is_nothrow_default_constructible<container_type>::value)
        : c() {}

    _LIBCPP_INLINE_VISIBILITY
    queue(const queue& __q) : c(__q.c) {}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    queue(queue&& __q)
        _NOEXCEPT_(is_nothrow_move_constructible<container_type>::value)
        : c(_VSTD::move(__q.c)) {}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    queue& operator=(const queue& __q) {c = __q.c; return *this;}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    queue& operator=(queue&& __q)
        _NOEXCEPT_(is_nothrow_move_assignable<container_type>::value)
        {c = _VSTD::move(__q.c); return *this;}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    explicit queue(const container_type& __c)  : c(__c) {}
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    explicit queue(container_type&& __c) : c(_VSTD::move(__c)) {}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        explicit queue(const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0)
            : c(__a) {}
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        queue(const queue& __q, const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0)
            : c(__q.c, __a) {}
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        queue(const container_type& __c, const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0)
            : c(__c, __a) {}
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        queue(container_type&& __c, const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0)
            : c(_VSTD::move(__c), __a) {}
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        queue(queue&& __q, const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0)
            : c(_VSTD::move(__q.c), __a) {}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    bool      empty() const {return c.empty();}
    _LIBCPP_INLINE_VISIBILITY
    size_type size() const  {return c.size();}

    _LIBCPP_INLINE_VISIBILITY
    reference       front()       {return c.front();}
    _LIBCPP_INLINE_VISIBILITY
    const_reference front() const {return c.front();}
    _LIBCPP_INLINE_VISIBILITY
    reference       back()        {return c.back();}
    _LIBCPP_INLINE_VISIBILITY
    const_reference back() const  {return c.back();}

    _LIBCPP_INLINE_VISIBILITY
    void push(const value_type& __v) {c.push_back(__v);}
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void push(value_type&& __v)      {c.push_back(_VSTD::move(__v));}
#ifndef _LIBCPP_HAS_NO_VARIADICS
    template <class... _Args>
        _LIBCPP_INLINE_VISIBILITY
#if _LIBCPP_STD_VER > 14
        reference emplace(_Args&&... __args)
            { return c.emplace_back(_VSTD::forward<_Args>(__args)...);}
#else
        void     emplace(_Args&&... __args)
            {        c.emplace_back(_VSTD::forward<_Args>(__args)...);}
#endif
#endif  // _LIBCPP_HAS_NO_VARIADICS
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void pop() {c.pop_front();}

    _LIBCPP_INLINE_VISIBILITY
    void swap(queue& __q)
        _NOEXCEPT_(__is_nothrow_swappable<container_type>::value)
    {
        using _VSTD::swap;
        swap(c, __q.c);
    }

    template <class _T1, class _C1>
    friend
    _LIBCPP_INLINE_VISIBILITY
    bool
    operator==(const queue<_T1, _C1>& __x,const queue<_T1, _C1>& __y);

    template <class _T1, class _C1>
    friend
    _LIBCPP_INLINE_VISIBILITY
    bool
    operator< (const queue<_T1, _C1>& __x,const queue<_T1, _C1>& __y);
};

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator==(const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y)
{
    return __x.c == __y.c;
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator< (const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y)
{
    return __x.c < __y.c;
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator!=(const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y)
{
    return !(__x == __y);
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator> (const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y)
{
    return __y < __x;
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator>=(const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y)
{
    return !(__x < __y);
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
bool
operator<=(const queue<_Tp, _Container>& __x,const queue<_Tp, _Container>& __y)
{
    return !(__y < __x);
}

template <class _Tp, class _Container>
inline _LIBCPP_INLINE_VISIBILITY
typename enable_if<
    __is_swappable<_Container>::value,
    void
>::type
swap(queue<_Tp, _Container>& __x, queue<_Tp, _Container>& __y)
    _NOEXCEPT_(_NOEXCEPT_(__x.swap(__y)))
{
    __x.swap(__y);
}

template <class _Tp, class _Container, class _Alloc>
struct _LIBCPP_TEMPLATE_VIS uses_allocator<queue<_Tp, _Container>, _Alloc>
    : public uses_allocator<_Container, _Alloc>
{
};

template <class _Tp, class _Container = vector<_Tp>,
          class _Compare = less<typename _Container::value_type> >
class _LIBCPP_TEMPLATE_VIS priority_queue
{
public:
    typedef _Container                               container_type;
    typedef _Compare                                 value_compare;
    typedef typename container_type::value_type      value_type;
    typedef typename container_type::reference       reference;
    typedef typename container_type::const_reference const_reference;
    typedef typename container_type::size_type       size_type;
    static_assert((is_same<_Tp, value_type>::value), "" );

protected:
    container_type c;
    value_compare comp;

public:
    _LIBCPP_INLINE_VISIBILITY
    priority_queue()
        _NOEXCEPT_(is_nothrow_default_constructible<container_type>::value &&
                   is_nothrow_default_constructible<value_compare>::value)
        : c(), comp() {}

    _LIBCPP_INLINE_VISIBILITY
    priority_queue(const priority_queue& __q) : c(__q.c), comp(__q.comp) {}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    priority_queue(priority_queue&& __q)
        _NOEXCEPT_(is_nothrow_move_constructible<container_type>::value &&
                   is_nothrow_move_constructible<value_compare>::value)
        : c(_VSTD::move(__q.c)), comp(_VSTD::move(__q.comp)) {}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    priority_queue& operator=(const priority_queue& __q)
        {c = __q.c; comp = __q.comp; return *this;}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    priority_queue& operator=(priority_queue&& __q)
        _NOEXCEPT_(is_nothrow_move_assignable<container_type>::value &&
                   is_nothrow_move_assignable<value_compare>::value)
        {c = _VSTD::move(__q.c); comp = _VSTD::move(__q.comp); return *this;}
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    explicit priority_queue(const value_compare& __comp)
        : c(), comp(__comp) {}
    _LIBCPP_INLINE_VISIBILITY
    priority_queue(const value_compare& __comp, const container_type& __c);
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    explicit priority_queue(const value_compare& __comp, container_type&& __c);
#endif
    template <class _InputIter>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(_InputIter __f, _InputIter __l,
                       const value_compare& __comp = value_compare());
    template <class _InputIter>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(_InputIter __f, _InputIter __l,
                       const value_compare& __comp, const container_type& __c);
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    template <class _InputIter>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(_InputIter __f, _InputIter __l,
                       const value_compare& __comp, container_type&& __c);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        explicit priority_queue(const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0);
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(const value_compare& __comp, const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0);
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(const value_compare& __comp, const container_type& __c,
                       const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0);
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(const priority_queue& __q, const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0);
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(const value_compare& __comp, container_type&& __c,
                       const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0);
    template <class _Alloc>
        _LIBCPP_INLINE_VISIBILITY
        priority_queue(priority_queue&& __q, const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type* = 0);
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

    _LIBCPP_INLINE_VISIBILITY
    bool            empty() const {return c.empty();}
    _LIBCPP_INLINE_VISIBILITY
    size_type       size() const  {return c.size();}
    _LIBCPP_INLINE_VISIBILITY
    const_reference top() const   {return c.front();}

    _LIBCPP_INLINE_VISIBILITY
    void push(const value_type& __v);
#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void push(value_type&& __v);
#ifndef _LIBCPP_HAS_NO_VARIADICS
    template <class... _Args> _LIBCPP_INLINE_VISIBILITY void emplace(_Args&&... __args);
#endif
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES
    _LIBCPP_INLINE_VISIBILITY
    void pop();

    _LIBCPP_INLINE_VISIBILITY
    void swap(priority_queue& __q)
        _NOEXCEPT_(__is_nothrow_swappable<container_type>::value &&
                   __is_nothrow_swappable<value_compare>::value);
};

template <class _Tp, class _Container, class _Compare>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(const _Compare& __comp,
                                                          const container_type& __c)
    : c(__c),
      comp(__comp)
{
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(const value_compare& __comp,
                                                          container_type&& __c)
    : c(_VSTD::move(__c)),
      comp(__comp)
{
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
template <class _InputIter>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(_InputIter __f, _InputIter __l,
                                                          const value_compare& __comp)
    : c(__f, __l),
      comp(__comp)
{
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

template <class _Tp, class _Container, class _Compare>
template <class _InputIter>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(_InputIter __f, _InputIter __l,
                                                          const value_compare& __comp,
                                                          const container_type& __c)
    : c(__c),
      comp(__comp)
{
    c.insert(c.end(), __f, __l);
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
template <class _InputIter>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(_InputIter __f, _InputIter __l,
                                                          const value_compare& __comp,
                                                          container_type&& __c)
    : c(_VSTD::move(__c)),
      comp(__comp)
{
    c.insert(c.end(), __f, __l);
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
template <class _Alloc>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type*)
    : c(__a)
{
}

template <class _Tp, class _Container, class _Compare>
template <class _Alloc>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(const value_compare& __comp,
                                                          const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type*)
    : c(__a),
      comp(__comp)
{
}

template <class _Tp, class _Container, class _Compare>
template <class _Alloc>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(const value_compare& __comp,
                                                          const container_type& __c,
                                                          const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type*)
    : c(__c, __a),
      comp(__comp)
{
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

template <class _Tp, class _Container, class _Compare>
template <class _Alloc>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(const priority_queue& __q,
                                                          const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type*)
    : c(__q.c, __a),
      comp(__q.comp)
{
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
template <class _Alloc>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(const value_compare& __comp,
                                                          container_type&& __c,
                                                          const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type*)
    : c(_VSTD::move(__c), __a),
      comp(__comp)
{
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

template <class _Tp, class _Container, class _Compare>
template <class _Alloc>
inline
priority_queue<_Tp, _Container, _Compare>::priority_queue(priority_queue&& __q,
                                                          const _Alloc& __a,
                       typename enable_if<uses_allocator<container_type,
                                                         _Alloc>::value>::type*)
    : c(_VSTD::move(__q.c), __a),
      comp(_VSTD::move(__q.comp))
{
    _VSTD::make_heap(c.begin(), c.end(), comp);
}

#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
inline
void
priority_queue<_Tp, _Container, _Compare>::push(const value_type& __v)
{
    c.push_back(__v);
    _VSTD::push_heap(c.begin(), c.end(), comp);
}

#ifndef _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
inline
void
priority_queue<_Tp, _Container, _Compare>::push(value_type&& __v)
{
    c.push_back(_VSTD::move(__v));
    _VSTD::push_heap(c.begin(), c.end(), comp);
}

#ifndef _LIBCPP_HAS_NO_VARIADICS

template <class _Tp, class _Container, class _Compare>
template <class... _Args>
inline
void
priority_queue<_Tp, _Container, _Compare>::emplace(_Args&&... __args)
{
    c.emplace_back(_VSTD::forward<_Args>(__args)...);
    _VSTD::push_heap(c.begin(), c.end(), comp);
}

#endif  // _LIBCPP_HAS_NO_VARIADICS
#endif  // _LIBCPP_HAS_NO_RVALUE_REFERENCES

template <class _Tp, class _Container, class _Compare>
inline
void
priority_queue<_Tp, _Container, _Compare>::pop()
{
    _VSTD::pop_heap(c.begin(), c.end(), comp);
    c.pop_back();
}

template <class _Tp, class _Container, class _Compare>
inline
void
priority_queue<_Tp, _Container, _Compare>::swap(priority_queue& __q)
        _NOEXCEPT_(__is_nothrow_swappable<container_type>::value &&
                   __is_nothrow_swappable<value_compare>::value)
{
    using _VSTD::swap;
    swap(c, __q.c);
    swap(comp, __q.comp);
}

template <class _Tp, class _Container, class _Compare>
inline _LIBCPP_INLINE_VISIBILITY
typename enable_if<
    __is_swappable<_Container>::value
    && __is_swappable<_Compare>::value,
    void
>::type
swap(priority_queue<_Tp, _Container, _Compare>& __x,
     priority_queue<_Tp, _Container, _Compare>& __y)
    _NOEXCEPT_(_NOEXCEPT_(__x.swap(__y)))
{
    __x.swap(__y);
}

template <class _Tp, class _Container, class _Compare, class _Alloc>
struct _LIBCPP_TEMPLATE_VIS uses_allocator<priority_queue<_Tp, _Container, _Compare>, _Alloc>
    : public uses_allocator<_Container, _Alloc>
{
};

_LIBCPP_END_NAMESPACE_STD

#endif  // _LIBCPP_QUEUE

``` 

## Ссылки на репозитории с реализацией контейнеров STL на дргуих языках

```ShellSession
https://github.com/grantjenks/python-sortedcontainers // контейнеры на Python (list, set, map)
https://github.com/haskell/containers/tree/master/benchmarks // контейнеры на Haskell (list, map, set)
https://github.com/pmatiello/python-graph // граф на Python
http://qaru.site/questions/16983/java-tree-data-structure // дерево на Java
http://qaru.site/questions/62517/how-can-i-implement-a-tree-in-python-are-there-any-built-in-data-structures-in-python-like-in-java // дерево на Python
https://github.com/learn-co-students/cs-implementing-an-arraylist-lab-codeU // List на Java
```
