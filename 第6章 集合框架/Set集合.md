set集合

map

元素是Entry< k, v >，Entry就是将key和value捆绑在一起

当一个set的元素是Entry的时候，该set就是一个map

hash()算法可以快速的计算出存、取的地址，存和取元素的效率很高，但是hash算法不能表示出元素之间的关系。hash算法决定了集合是无序的。

数组+链表，bucket桶结构，hash冲突

HashSet底层实现是HashMap

TreeMap

红黑树，平衡二叉树，排序二叉树（二叉查找树，左小右大，中序有序），有序，自然排序compareTo()方法，比较器排序Comparator接口

Entry< key, value >