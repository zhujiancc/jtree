<template>
  <li :class="{folder:isFolder}">
    <div :class="{bold: isFolder}">
      <span class="collapse">
        <span v-if="isFolder"  @click="toggle" :class="open?'fa fa-minus-square-o':'fa fa-plus-square-o'" ></span>
        <span v-else class="fill"></span>
        <input type="checkbox" v-model="model.check" @change="toggleCheck(model)" > 
      </span>
      <span  @click="toggle" >
        <input v-model="model.name" v-if="edit" @blur="edit=false"  @keypress.enter="edit=!edit" v-focus>
        <span v-else  @click="selected(model)" @dblclick="addChild(model)">{{model.name}}</span>
      </span>
      <span @click="edit=!edit" v-if="options.edit" :title="lang.edit" class="fa fa-edit"></span>
      <span @click="del(model)" v-if="options.del" :title="lang.delete" class="fa fa-times"></span>
    </div>
    <ul  v-if="isFolder&&open">
      <node class="tree"  v-for="model in model.children" :model="model" :options="options" :lang="lang" :key="model.id"></node>
      <li class="add" @click="addSibling(model)"><i>{{lang.addNode}}</i></li>
    </ul>
  </li>
</template>

<script>
import Bus from "./Bus";
import shortid from "js-shortid";
export default {
  name: "node",
  directives:{
    focus:{
      inserted(el){
        el.focus()
      }
    }
  },
  props: {
    model: Object,
    options: {
      type: Object,
      default() {
        return {};
      }
    },
    lang:Object
  },
  data() {
    return {
      open: false,
      edit: false
    };
  },
  watch: {
    edit(value) {
      if (!value && !this.model.name) {
        alert(this.lang.empty);
        this.edit = true;
      }
    }
  },
  computed: {
    isFolder() {
      return this.model.children && this.model.children.length;
    }
  },
  methods: {
    toggle() {
      if (this.isFolder && !this.edit) {
        this.open = !this.open;
      }
    },
    toggleCheck(item) {
      Bus.$emit("check", item);
    },
    addChild(item) {
      if (!this.isFolder) {
        this.$set(this.model, "children", []);
        this.addSibling(item);
        this.open = true;
      }
    },
    addSibling(item) {
      this.model.children.push({
        id: shortid.gen(),
        name: this.lang.newNode,
        check: item.check,//hack 
        children: []
      });
    },
    selected(item) {
      Bus.$emit("selected", item);
    },
    del(item) {
      if (!confirm("确认删除?")) {
        return;
      }
      if (!this.$parent.model) {
        let tree = this.$parent.tree;
        tree.splice(tree.indexOf(item), 1);
      } else {
        let cs = this.$parent.model.children;
        cs.splice(cs.indexOf(item), 1);
      }
    }
  }
};
</script>
