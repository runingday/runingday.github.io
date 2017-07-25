## 1. hashable

    An object is hashable if it has a hash value which never changes during its lifetime (it needs a __hash__() method), and can be compared to other objects (it needs an __eq__() method). Hashable objects which compare equal must have the same hash value.

    Hashability makes an object usable as a dictionary key and a set member, because these data structures use the hash value internally.

    All of Python’s immutable built-in objects are hashable; mutable containers (such as lists or dictionaries) are not. Objects which are instances of user-defined classes are hashable by default. They all compare unequal (except with themselves), and their hash value is derived from their id().
可哈希

    如果一个对象是哈希值，它在其生命周期内永远不会改变（它需要一个__hash __（）方法），并且可以与其他对象进行比较（需要一个__eq __（）方法）。 比较相等的Hashable对象必须具有相同的哈希值。

    Hashability使对象可用作字典键和集成员，因为这些数据结构在内部使用散列值。

    所有Python的不可变内置对象都是可以散列的; 可变容器（例如列表或字典）不是。 默认情况下，作为用户定义类的实例的对象是可散列的。 他们都比较不等（除了自己），它们的哈希值是从它们的id（）派生出来的。
    
 ## 2. dictionary 
 In [1]: a = dict(one=1, two=2, three=3)

In [2]: b = {'one':1, 'two': 2, 'three': 3}

In [3]: c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))

In [4]: d = dict([('two', 2), ('one', 1), ('three', 3)])

In [5]: e = dict({'three': 3, 'one': 1, 'two': 2})
