---
layout: post
title: Doubly Linked List 를 만들어 보자 1
comments: true
tags:
- Python
- DataStructure
- TIL
---

TDD 연습삼아 Doubly Linked List 를 차근차근 구현해 봤습니다.
포스팅의 목적은 구현한 코드 그리고 구현을 하면서 생겼던 궁금증을 공유하는 것입니다.

### Test와 함께 LinkedList의 Node 구현하기

Doubly Linked List 의 Node 는 다음과 같은 구조로 구현하겠습니다.

Doubly Linked List Node ![alt text](/images/linkedList/node.png)

`prev` : 이전 Node 를 가르키는 포인터
`item` : Node 의 데이터
`next` : 다음 Node 를 가르키는 포인터

일단 Test를 정의하겠습니다.

``` Python
import unittest


class NodeTests(unittest.TestCase):

    def test_init_node(self):
        node = Node(1)

        self.assertEqual(1, node.item, "node 의 item 은 1 이어야 한다.")
        self.assertIsNone(node.prev_node, "node 의 prev 기본값은 None 이어야한다")
        self.assertIsNone(node.next_node, "node 의 next 기본값은 None 이어야한다")


if __name__ == '__main__':
    unittest.main()
```

실행을 해보면 당연히 실패 Error가 나올 것 입니다. Node 라는 클래스를 정의하지 않았으니까요.

```
Ran 1 test in 0.002s

FAILED (errors=1)

Error
Traceback (most recent call last):
    ...
    node = Node(1)
NameError: name 'Node' is not defined
```

Node class 를 정의해 주고, 테스트가 통과할말한 가장 간단한 코드를 작성 후 테스트를 해보겠습니다.

``` python
class Node:

    def __init__(self, item):
        self.item = item
        self.prev_node = None
        self.next_node = None
```
```
Ran 1 test in 0.001s

OK
```

딱히 리팩토링할 내용이 없어보였습니다.

Node를 생성할 때 이전 포인터와 다음 포인터를 같이 매개변수로 같이 넘기고 싶어서 다음과 같은 테스트를 하나 정의하겠습니다

```python
def test_init_node_with_prev_and_next(self):
    prev_node = Node(0)
    next_node = Node(1)

    node = Node(1, prev_node, next_node)
    self.assertEqual(prev_node, node.prev_node, "node 의 prev 는 prev_node 를 가르키고 있어야 한다.")
    self.assertEqual(next_node, node.next_node, "node 의 prev 는 prev_node 를 가르키고 있어야 한다.")
```
```
Error
Traceback (most recent call last):
    ...
    node = Node(1, prev_node, next_node)
TypeError: __init__() takes 2 positional arguments but 4 were given
```
Error가 발생했습니다.

Node class 의 `__init__` 메서드를 수정해서 성공시켰습니다.

``` python
class Node:

    def __init__(self, item, prev_node=None, next_node=None):
        self.item = item
        self.prev_node = prev_node
        self.next_node = next_node
```

```
Ran 2 tests in 0.001s

OK
```

음 리팩토링할 코드가 있을까요??? 잘 모르겠습니다. 없는것 같습니다.

Node 를 생성할 때
``` Python
node = Node()
```
이렇게 item 에 대한 인자 없이 생성하게 되면 `TypeError` 가 발생하는데요.
이 Error에 관해서도 테스트 했습니다.

```python
def test_init_node_error_when_empty_item_param(self):
    self.assertRaises(TypeError, Node,
                      msg="item parameter 를 넘기지 않으면 TypeError 가 발생해야한다")
    self.assertRaises(TypeError, Node, **{"prev": Node(1), "next": Node(2)},
                      msg="item parameter 를 넘기지 않으면 TypeError 가 발생해야한다")
```
```
Ran 3 tests in 0.002s

OK
```

요렇게요. 코드를 따로 변경하지 않았는데도 통과를 했습니다. 이러한 테스트 (당연히 발생하는 Error 이고 테스트가 필요 없을거 같은 테스트)를 작성한 이유는 협업시의 저의 의도 (Node 를 생성하려면 무조건 item 은 있어야 한다.) 를 동료들에게 정확히 전달하기 위해서 입니다.

Node에 관해서 더 구현할게 없는거 같습니다. 다음으로는 아래과 같은 구조로 Doubly Linked List 를 만들어 봤습니다.

Doubly Linked List ![alt text](/images/linkedList/ddl.png)

### Doubly Linked List ADT
간단하게 다음과 같은 기능을 구현했습니다.
* _len :  리스트의 길이 저장
* node(at: ) : 특정 위치 노드 참조
* append() : 리스트 맨 뒤에 노드 추가
* insert(at : ) : 리스트 특정 위치에 노드 추가
* remove(at : ) : 특정 위치 노드 삭제


리스트 생성 Test
```python
import unittest


class DoublyLinkedListTests(unittest.TestCase):

    def test_init_DLList(self):
        dllist = DoublyLinkedList()
        self.assertIsNone(dllist.head)
        self.assertIsNone(dllist.tail)
        self.assertEqual(len(self.dllist), 0)


if __name__ == '__main__':
    unittest.main()
```
아직 DoublyLinkedList 클래스를 정의하지 않았습니다. 정의하겠습니다.

클래스 정의

```python
class DoublyLinkedList:

    def __init__(self):
        self.head = None
        self.tail = None
        self._len = 0
```
len() 함수를 사용하기 위해서는 `__len__` 매직 메서드를 구현해야 합니다.

```python
def __len__(self):
    return self._len
```

테스트를 돌려봤습니다.

```
Ran 1 test in 0.001s

OK
```
성공했습니다. 리팩토링?? 아직 할 게 없어 보입니다.

Node 를 추가하는 테스트를 작성해 보겠습니다.

``` python
def test_append_one_node(self):
    self.dllist.append(0)
    self.assertEqual(self.dllist.head.item, 0)
    self.assertEqual(self.dllist.tail.item, 0)
    self.assertEqual(len(self.dllist), 1)
```
append 정의
``` python
def append(self, node):
    pass
```
```
Error
Traceback (most recent call last):
    ...
    self.assertEqual(self.dllist.head.item, 0)
AttributeError: 'NoneType' object has no attribute 'item'
```
Error 가 발생했습니다 append 함수에서 아무것도 안하기 때문입니다.

append 함수를 간단하게 구현했습니다.
``` python
def append(self, node):
    self.head = self.tail = Node(item)
    self._len += 1
```
```
Ran 2 tests in 0.001s

OK
```
성공 했습니다. 리팩토링을 진행했습니다.

```python
class DoublyLinkedListTests(unittest.TestCase):

    def setUp(self):
        self.dllist = DoublyLinkedList()

    def test_init_DLList(self):
        self.assertIsNone(self.dllist.head)
        self.assertIsNone(self.dllist.tail)
        self.assertEqual(len(self.dllist), 0)

    def test_append_one_node(self):
        self.dllist.append(0)
        self.assertEqual(self.dllist.head.item, 0)
        self.assertEqual(self.dllist.tail.item, 0)
        self.assertEqual(len(self.dllist), 1)
```
dllist 를 생성하는 코드를 setUp 함수로 옮겼습니다. 이제 테스트 마다 dllist 를 생성하지 않아도 됩니다.

append 기능을 완전히 구현하기 위해 또 다른 테스트를 작성했습니다.

```python
def test_append_two_node(self):
      self.dllist.append(0)
      self.dllist.append(1)
      self.assertEqual(self.dllist.head.item, 0)
      self.assertEqual(self.dllist.tail.item, 1)
      self.assertEqual(self.dllist.head.next_node.item, 1)
      self.assertEqual(self.dllist.tail.prev_node.item, 0)
      self.assertEqual(len(self.dllist), 2)
```
```
0 != 1

Expected :1
Actual   :0
 <Click to see difference>
```

head 와 tail 이 None 일때와 아닐 때를 구분해서 append를 수정했습니다.

```python
def append(self, item):
    new_node = Node(item)
    if not self.head and not self.tail:
        self.head = self.tail = new_node
    else:
        new_node.prev_node = self.tail
        self.tail.next_node = new_node
        self.tail = new_node
    self._len += 1
```
```
Ran 3 tests in 0.002s

OK
```
성공 했습니다.

head node와 tail node 사이에 하나의 노드가 더 생기도록 하고 테스트를 한번더 진행해 봤습니다.
```python
def test_append_three_node(self):
      self.dllist.append(0)
      self.dllist.append(1)
      self.dllist.append(2)
      self.assertEqual(self.dllist.head.item, 0)
      self.assertEqual(self.dllist.tail.item, 2)
      self.assertEqual(self.dllist.head.next_node.item, 1)
      self.assertEqual(self.dllist.head.next_node.prev_node.item, 0)
      self.assertEqual(self.dllist.tail.prev_node.item, 1)
      self.assertEqual(self.dllist.tail.prev_node.next_node.item, 2)
      self.assertEqual(len(self.dllist), 3)
```
```
Ran 4 tests in 0.002s

OK
```
따로 코드를 수정하지 않았는데 성공 했습니다. 이제 append기능은 완성된거 같습니다.
중간에 리팩토링 작업을 하지 않았습니다. node에 다수 item 을 append 하는 함수를 만들면 코드를 줄일수 있을거 같습니다.


```python
def test_append_two_node(self):
    self.append_items([0, 1])
    ...

def test_append_three_node(self):
    self.append_items([0, 1, 2])
    ...

def append_items(self, items):
    for item in items:
        self.dllist.append(item)
```

다음으로는 특정 위치의 노드를 참조하는 node() 함수를 만들어 보겠습니다. 그리고 특정 위치 노드의 아이템에 바로 접근하기 위해 인덱싱 기능도 넣어 보겠습니다.

```python
def test_access_node(self):
    self.append_items([1, 2, 3])
    self.assertEqual(self.dllist.node(0).item, 1)
    self.assertEqual(self.dllist.node(1).item, 2)
    self.assertEqual(self.dllist.node(2).item, 3)
    self.assertEqual(self.dllist.node(-1).item, 3)
    self.assertEqual(self.dllist.node(-2).item, 2)
    self.assertEqual(self.dllist.node(-3).item, 1)
```

당연히 node 함수를 만들지 않았으니 Error가 발생하겠죠? node 함수를 정의하고 실패하는 코드를 작성했습니다.

```python
class DoublyLinkedList:
  ...
  def node(self, 1):
      return Node(1)

```

```
2 != 1

Expected :1
Actual   :2
```
다음은 성공하는 코드를 작성했습니다.
``` python
def node(self, idx):
    if idx < 0:
        cur = self.tail
        for i in range(abs(idx) - 1):
            cur = cur.prev_node
        return cur
    else:
        cur = self.head
        for i in range(idx):
            cur = cur.next_node
        return cur
```
만약 존재하지 않는 노드를 접근하려 하면 Error를 발생시켜야 하니 그 구현에 대한 테스트를 작성했습니다.

```python
def test_index_error_when_accessing_None(self):
    self.assertRaises(IndexError, self.dllist.node, 0)
    self.append_items([1, 2, 3])
    self.assertRaises(IndexError, self.dllist.node, 3)
    self.assertRaises(IndexError, self.dllist.node, -4)
```
IndexError 를 발생시키지 않으니 당연히 실패입니다.
```
Ran 1 test in 0.002s

FAILED (failures=1)

Failure
Traceback (most recent call last):
  ....
AssertionError: IndexError not raised by node
```
_len 프로퍼티를 사용해 idx 체크를 해주고, 올바르지 않은 인덱스이면 IndexError를 발생시켰습니다.
``` python
def node(self, idx):
    if not -self._len <= idx < self._len:
        raise IndexError
    ...
```
```
Ran 6 tests in 0.002s

OK
```
list 처럼 인덱싱이 가능하도록 `__getitem__` 메서드도 만들어 주고 싶습니다. 테스트를 통해 진행했습니다.

```python
def test_access_by_indexing(self):
    self.append_items([1, 2, 3])
    self.assertEqual(self.dllist[0], 1)
    self.assertEqual(self.dllist[1], 2)
    self.assertEqual(self.dllist[2], 3)
    self.assertEqual(self.dllist[-1], 3)
    self.assertEqual(self.dllist[-2], 2)
    self.assertEqual(self.dllist[-3], 1)
```
```
Error
Traceback (most recent call last):
...
TypeError: 'DoublyLinkedList' object does not support indexing
```
실패 테스트를 작성하고 `__getitem__`을 작성합니다.

```python
def __getitem__(self, idx):
    return self.node(idx).item
```

인덱싱을 써서 IndexError를 유도하게 하는 테스트는 어떻게 해야 좋을까요?
두 가지 방법이 생각이 났습니다.

```python
# 첫번째 방법
def test_index_error_when_accessing_None_by_indexing(self):
    self.append_items([1, 2, 3])
    try:
        self.dllist[3]
    except IndexError:
        self.assertTrue(True)
    else:
        self.assertTrue(False)
    try:
        self.dllist[-4]
    except IndexError:
        self.assertTrue(True)
    else:
        self.assertTrue(False)

# 두번째 방법
def test_index_error_when_accessing_None_by_indexing(self):
    self.append_items([1, 2, 3])
    self.assertRaises(IndexError, self.dllist.__getitem__, 3)
    self.assertRaises(IndexError, self.dllist.__getitem__, -4)
```

두번째 방법이 깔끔해 보이네요. 두번째 방법으로 하겠습니다.

글이 길어지는거 같으니 다음 포스팅에서 insert와, remove 함수를 구현해 보겠습니다.
