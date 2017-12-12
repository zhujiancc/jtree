<template>
  <div class="trees">
      <button  class="add-root" @click="addSibling"><strong><i>{{lang.addRoot}}</i></strong></button>
      <ul  v-for="model in tree" :key="model.id"> 
        <node class="tree" :model="model" :options="options" :lang="lang"></node>
      </ul>
  </div>
</template>

<script>
import Node from "./Node";
import Bus from "./Bus";
import shortid from "js-shortid";
import "font-awesome/css/font-awesome.min.css";
export default {
  props: {
    tree: Array,
    options: Object,
    lang: {
      type: Object,
      default() {
        return {
          addRoot: "新增根节点",
          delete: "删除",
          edit: "编辑",
          addNode:"添加新节点",
          newNode:"子节点",
          newRoot:"根节点",
          empty:"内容不可为空!!"
        };
      }
    }
  },
  components: {
    Node
  },
  mounted() {
    let self = this;
    Bus.$on("check", item => {
      self.update(item);
    });
    Bus.$on("selected", item => {
      self.$emit("current_node", item);
    });
  },
  methods: {
    update(item) {
      function toBack(item) {
        if (item.children && item.children.length) {
          let check = item.check;
          for (let i = 0; i < item.children.length; i++) {
            item.children[i].check = check;
            toBack(item.children[i]);
          }
        }
      }
      function toFront(data) {
        if (data.children && data.children.length) {
          let checkedLength = 0;
          for (let i = 0; i < data.children.length; i++) {
            let node = data.children[i];
            if (node.children && node.children.length) node = toFront(node);
            if (node.check) checkedLength++;
          }
          data.check = checkedLength >= data.children.length;
          return data;
        } else return data;
      }
      toBack(item);
      let model = this.tree;
      for (let i = 0; i < model.length; i++) {
        toFront(model[i]);
      }
    },
    addSibling() {
      this.tree.push({
        id: shortid.gen(),
        name: this.lang.newRoot,
        check: false,
        children: []
      });
    }
  }
};
</script>

<style>
body {
  font-family: Menlo, Consolas, monospace;
  color: #444;
}
.trees ul {
  list-style-type: none;
  padding-left: 20px;
  line-height: 24px;
  cursor: pointer;
  position: relative;
}
.collapse > input {
  margin: 1px;
}
.add-root {
  margin-left: 20px;
}
.collapse > span {
  float: left;
  margin: 2px;
}
.add {
  margin-left: 40px;
  color: green;
}

.fill {
  width: 13px;
}
.tree li {
  line-height: 24px;
}
.bold {
  font-weight: bold;
}
.trees ul > li > ul > li {
  position: relative;
}
.tree li:before {
  display: block;
  content: "";
  border-left: 1px solid #999;
  height: 100%;
  top: -8px;
  position: absolute;
  left: -17px;
}
.tree li:after {
  display: block;
  content: "";
  border-top: 1px solid #999;
  top: 10px;
  width: 35px;
  position: absolute;
  left: -16px;
}
.folder:after {
  width: 18px !important;
}

.tree li:last-child::after {
  left: -57px;
  width: 55px;
}
.tree li:last-child::before {
  left: -57px;
  top: -13px;
}
</style>

