---
title: P146.LRU缓存
date: 2025-10-03 14:57:17
tags: [leetcode,链表,LRU,Map]
categories: [算法]
---
<meta name="referrer" content="no-referrer"/>

# 题目
请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
-  LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
- int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
- void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

# 解析
通过一个双向链表+map实现LRU，双向链表设置一个哨兵节点，用于快速插入和快速删除。

put方法：

+ 如果已经在map，就直接改变值，然后将节点放到头部
+ 如果不存在
    - 放到map
    - 节点放到前面
    - 判断数量，如果超过容量，就去掉尾部节点

get方法：

+ 删除该节点在链表位置
+ 将节点添加到头部

![画板](https://cdn.nlark.com/yuque/0/2024/jpeg/44165525/1728523029753-3068ab2b-7210-47ef-963a-044e84f1d551.jpeg)

# 代码
```java
/**
 * 思路：利用双指针的O(1)删除和插入，然后利用哈希表的查找O(1)
 * 将所有操作都变为O(1)
 */
class LRUCache {

    private class ListNode{
        int key,value;
        ListNode pre,next;

        public ListNode() {
        }

        public ListNode(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private int count;  // 节点数量
    private Map<Integer, ListNode> map; // map集合
    private ListNode head; // 哨兵节点
    private int capacity;  // 容量

    public LRUCache(int capacity) {
        count=0;
        map=new HashMap<>();
        head=new ListNode();
        head.pre=head;
        head.next=head;
        this.capacity=capacity;
    }

    public int get(int key) {
        if(!map.containsKey(key)) return -1;
        // 取出元素
        ListNode listNode = map.get(key);
        // 删除节点
        removeNode(listNode);
        // 添加节点到头部
        addNode(listNode);
        return listNode.value;
    }

    public void put(int key, int value) {
        // 存在，直接取出，然后添加，删除和放到头部
        if(map.containsKey(key)){
            ListNode listNode = map.get(key);
            listNode.value=value;
            removeNode(listNode);
            addNode(listNode);
            return ;
        }

        ListNode node=new ListNode(key,value);
        addNode(node);
        map.put(key,node);
        count++;

        // 空间不够减去尾部
        if(count>=capacity){
            map.remove(head.pre.key);
            removeNode(head.pre);
            count--;
        }
    }

    // 删除节点
    private void removeNode(ListNode node){
        node.pre.next=node.next;
        node.next.pre=node.pre;
    }

    // head是虚节点
    private void addNode(ListNode node){
        node.next=head.next;
        node.pre=head;
        node.next.pre=node;
        node.pre.next=node;
    }

}
```

# 总结
<font style="color:rgba(0, 0, 0, 0.85);">本 LRUCache 实现通过 “双向循环链表（含哨兵节点）+ 哈希表” 的组合方案，既利用哈希表实现键到节点的 O (1) 查找，又借助双向链表完成节点 O (1) 插入 / 删除以维护 “最近使用（头部）- 最久未使用（尾部）” 顺序；构造函数初始化缓存容量与基础结构，get 方法通过查找节点、删除后插入头部更新使用顺序并返回值，put 方法分 “键存在则更新值并移头部”“键不存在则新增节点、超容时淘汰尾部节点” 两种场景处理，辅助方法 removeNode 和 addNode 简化节点操作，最终实现 get 与 put 操作 O (1) 平均时间复杂度，同时满足 LRU 缓存的初始化、数据存取及超容淘汰核心需求，哨兵节点设计还避免了边界判断，提升代码简洁性。</font>

