- 为什么重写 equals() 时必须重写 hashCode() 方法？

  hashCode() 的作用是获取哈希码（int 整数），也称为散列码。这个哈希码的作用是**确定该对象在哈希表中的索引位置**。Object 的 hashCode() 方法是本地方法。

  hashCode()的目的是在HashSet、HashMap类似的数据结构中，快速查找对象。即当把对象加入 HashSet 时，HashSet 会先计算对象的 hashCode 值来判断对象加入的**位置**，同时也会与其他已经加入的对象的 hashCode 值作**比较**，如果没有相符的 hashCode，HashSet 会假设对象没有重复出现。但如果发现有相同 hashCode 值的对象，这时会调用 equals() 方法来检查 hashCode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样大大减少了 equals 的次数，相应提高了执行速度。

  - 如果两个对象的hashCode 值相等，那这两个对象不一定相等（哈希碰撞）。

  - 如果两个对象的hashCode 值相等并且equals()方法也返回 true，才认为这两个对象相等。

  - 如果两个对象的hashCode 值不相等，就可以直接认为这两个对象不相等。