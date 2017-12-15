# v-tree

> A Vue.js project of Tree

## How to use it 

```bash
npm install j-tree --save 

```

```js
import Tree from 'j-tree'

Vue.component('tree',Tree)
```

```html
<tree :tree="treeArr" :options="optionsObj"></tree>
```

## Props Introduce

* tree : A array data ,must contain 'id , name , check , children' like:
```js
tree:[
  {
    name:'root',
    check:false,
    id:1,
    children:[
      {
        name:'node',
        check:false,
        id:2
      }
    ]
  }
],
```

* options :  some tree setting =>
```js
{
  edit:true,// tree can be modified when it open
  del:true // tree can be deleted when it open 
}
``` 

* lang : set different languages=>
```js
//zh-cn
{
  addRoot: "新增根节点",
  delete: "删除",
  edit: "编辑",
  addNode:"添加新节点",
  newNode:"子节点",
  newRoot:"根节点",
  empty:"内容不可为空!!"
};
//en
{
  addRoot: "Add new root",
  delete: "Delete",
  edit: "Edit",
  addNode:"Add new node",
  newNode:"New Node",
  newRoot:"New Root",
  empty:"Content cannot be empty !"
}

```

---
 [More...](https://github.com/zhujiancc/jtree)