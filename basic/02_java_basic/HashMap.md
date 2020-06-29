# HashMap

如何确定位置？

```Java
static final int hash(Object key) {
    int h;
    // 异或（^）符号运算规则为相同为 0 ，不同为 1；无符号右移（>>>）高位都补零
    // 所以运算结果的高 16 位和 h 的高 16 位一致，低 16 位是 h 的高 16 位和低 16 位异或的结果
    //
    // 示例，假设 h = key.hashCode() = 44739312
    // 1. 44739312 二进制为 010101010101010101011110000
    // 2. (44739312 >>> 16) 二进制为 000000000000000001010101010
    // 3. 010101010101010101011110000
    //    000000000000000001010101010
    //    ^
    //    010101010101010100001011010
    // 4. 010101010101010100001011010 十进制为 44738650
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
            boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        // (n - 1) & hash 确定位置
        // 假设 n 为 16，hash 为上面的 44738650
        // (16 - 1) & 44738650
        // 000000000000000000000001111
        // 010101010101010100001011010
        // &
        // 000000000000000000000001010 --> 10
        tab[i] = newNode(hash, key, value, null);
    else {
        // 发生 hash 碰撞才会走到这里
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```