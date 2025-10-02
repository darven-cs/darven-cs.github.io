---
title: P25.k个一组反转链表
date: 2025-10-02 12:44:01
index_img: /img/leetcode.svg
tags: [leetcode,链表]
categories: [算法]
---
<meta name="referrer" content="no-referrer"/>
# 题目
给你链表的头节点 head ，每 k 个节点一组进行翻转，请你返回修改后的链表。

k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

![](https://cdn.nlark.com/yuque/0/2025/png/44165525/1759380354244-95225fa1-10a4-4386-9ce9-5fb6dfdc44d4.png)

![](https://cdn.nlark.com/yuque/0/2025/png/44165525/1759380369603-6334be71-1fdd-4b3e-bc8f-6502c17f35b8.png)

# 解析思路
1.通过找到start和end的区间，通过遍历寻找k个节点，也就是end的位置。

2.end的末尾截断，然后头插法反转start，end区间的链表。

3.通过新的链表接收这个反转区间

4.如果没有k的倍数，就直接跳出，然后pre指向start，也就是未处理的部分。

5.返回dummy即可。

# 代码
```java
public class P25 {

    public static void main(String[] args) {
        ListNode head=new ListNode(0);
        ListNode cur=head;
        for(int i=1;i<=5;i++){
            cur.next=new ListNode(i);
            cur=cur.next;
        }
        head=head.next;
        P25 p25 = new P25();
        ListNode node = p25.reverseKGroup(head, 3);
        while (node!=null){
            System.out.println(node.val);
            node=node.next;
        }
    }


    public ListNode reverseKGroup(ListNode head, int k) {
        /**
         * 先拆分，然后按照区间进行反转链表
         * */

        if(head==null||head.next==null){
            return head;
        }

        ListNode dummy=new ListNode(-1);
        ListNode pre=dummy;
        // 操作head
        ListNode start=head;
        ListNode end=head;

        while(end!=null){
            // 找到end边界，如果end为空就直接退出
            for(int i=1;i<k&&end!=null;i++){
                end=end.next;
            }
            if(end==null){
                break;
            }
            // 指向下一个
            ListNode next =end.next;
            // 截断区间
            end.next=null;
            // 区间反转
            pre.next=reverse(start);
            // 反转之后start变成最后一个
            pre=start;
            start=next;
            end=next;
        }
        // 接上start，避免没有倍数的部分
        pre.next=start;
        return dummy.next;
    }

    // 头插法
    private ListNode reverse(ListNode start) {
        ListNode pre=null;
        ListNode cur=start;

        while (cur!=null){
            ListNode next=cur.next;
            cur.next=pre;
            pre=cur;
            cur=next;
        }

        return pre;
    }
}
```

# 总结
时间复杂度最坏是O(n2),两个循环，然后空间复杂度是O(1),只使用了几个常量节点。这道题就是模拟就行，我看其他人还使用递归，这个后面看看。

