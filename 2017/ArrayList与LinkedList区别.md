# 底层实现

## ArrayList

底层是对象的数据，构造时按照定义的`initialCapacity`预先初始化对象的数组，每次执行`add`操作都是往数组对应的下标赋值赋值。
在当前当度将要超过数组长度的时候，会生成新的数组进行扩容，并将之前已经添加的元素全部拷贝进新的数组中。

## LinkedList

底层是双向链表结构。

# 应用场景

- 对于访问任意位置的数据：ArrayList效率更高，LinkedList每次都需要执行遍历，效率更低；
- 对于往后添加元素：ArrayList与LinkedList效率等同，但是当ArrayList满容时的添加，会导致数组的扩容，综合来看LinkedList效率更高；
- 对于对元素的删除：ArrayList需要将之后的所有元素往前移动，带来额外的开销，LinkedList效率更高；
- 空间占用：ArrayList因为预先初始化数组，会导致冗余空间，而LinkedList除了保留元素，还需要维护元素的引用关系，二者没有优劣。
