---
title: 合并k个有序链表
categories:
- 数据结构
---
```js
let mergeKLists = function(lists) {
        let arr = [] // 声明一个优先队列
        let newNode = new ListNode(-1)
        let res = newNode
        for (let list of lists) {
            if (list) {
                arr.push(list)
            }
        } // 保存所有链表的头结点

        arr.sort(mySort('val'))

        while (arr.length) { // 优先队列不为空时
            let node = arr.shift() // 最小的那个结点出栈
            newNode.next = node
            newNode = newNode.next
            if (node.next) { // 当前结点有后续的next结点
                arr.push(node.next)
                arr.sort(mySort('val')) // 重新排序
            }
        }

        function mySort (val) {
            return function (n1, n2) {
                let a = n1[val]
                let b = n2[val]
                return a - b
            }
        } // 根据结点的值进行排序

        return res.next
    }
```
