---
title: 创建二叉树
categories:
- 数据结构
---
#### 根据一个数组（二叉树的中序遍历），创建一颗二叉树
```js
    function treeNode() {
            this.val=null;
            this.left=null;
            this.right=null;
        } // 定义树节点

    let index=0;
    function creatTree(root,arr) {
        root.val=arr[index++];
        if(root.val==null){
            return;
        }
        root.left=new treeNode();
        creatTree(root.left,arr); // 递归创建左子树
        if(root.left.val==null){  
            root.left=null;  // 当前结点为空值，则没有左子结点
        }

        root.right=new treeNode();
        creatTree(root.right,arr); // 递归创建右子树
        if(root.right.val==null){
            root.right=null; // 当前结点为空值，则没有右子结点
        }
        return root;
    }

    let root=new treeNode();
    let arr=['A', 'B', 'D', null, null, 'E', null, null, 'C', 'F',null, null, 'G', null, null];
    console.log(creatTree(root,arr));
```
最后输出这样一个树结构：
```js
treeNode {val: "A", left: treeNode, right: treeNode}
-left: treeNode {val: "B", left: treeNode, right: treeNode}
-right: treeNode {val: "C", left: treeNode, right: treeNode}
```
