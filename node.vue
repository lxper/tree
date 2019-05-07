<template>
  <ul class="tree-ul">
    <li class="tree-li">
      <!-- 展开按钮 -->
      <span class="tree-expand" @click="handleExpand">
        <span v-if="data.children && data.children.length && !data.expand">+</span>
        <span v-if="data.children && data.children.length && data.expand">-</span>
      </span>
      <!-- 复选框 -->
      <m-checkbox
        v-if="showCheckbox"
        :isChecked="data.checked"
        @input="handleCheck"
        class="tree-checkbox"
      >
      </m-checkbox>
      <!-- 节点标题 -->
      <span>{{ data.title }}</span>
      <!-- 子节点 -->
      <div v-if="data.expand && data.children">
        <tree-node
        v-for="(item, index) in data.children"
        :key="index"
        :data="item"
        :show-checkbox="showCheckbox"
        ></tree-node>
      </div>
    </li>
  </ul>
</template>
<script>
  import MCheckbox from './mCheckbox.vue' //复选框组件
  function findComponentUpward (context, componentName) { //找到tree组件
    let parent = context.$parent;
    let name = parent.$options.name;
    while (parent && (!name || [componentName].indexOf(name) < 0)) {
      parent = parent.$parent;
      if (parent) name = parent.$options.name;
    }
    return parent;
  }
  export default {
    name: 'TreeNode',
    components: {MCheckbox},
    props: {
      data: {
        type: Object,
        default () {
          return {};
        }
      },
      showCheckbox: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        //获取tree，以便让tree的父组件响应事件
        tree: findComponentUpward(this, 'Tree'),
      }
    },
    methods: {
      //响应展开或关闭事件
      handleExpand () {
        this.$set(this.data, 'expand', !this.data.expand);
        if (this.tree) {
          //page.vue触发on-toggle-expand
          this.tree.emitEvent('on-toggle-expand', this.data); 
        }
      },
      //响应checkbox点击事件
      handleCheck (checked) {
        this.updateTreeDown(this.data, checked);
        if (this.tree) {
          //page.vue触发on-check-change
          this.tree.emitEvent('on-check-change', this.data);
        }
      },
      //递归修改子节点的checkbox
      updateTreeDown (data, checked) {
        this.$set(data, 'checked', checked);
        if (data.children && data.children.length) {
          data.children.forEach(item => {
            this.updateTreeDown(item, checked);
          });
        }
      }
    },
    watch: {
      'data.children': {
        handler (data) {
          let checked = 0;
          if (data) {
            for(let i in data){
              checked += data[i].checked;
            }
            checked === data.length? checked = true: checked = false;
            this.$set(this.data, 'checked', checked);
          }
        },
        deep: true
      }
    }
  }
</script>
<style>
  .tree-ul, .tree-li{
    list-style: none;
    padding-left: 10px;
  }
  .tree-expand{
    cursor: pointer;
    font-size: 1.5rem;
    display: inline-block;
  }
  .tree-checkbox{
    margin-right: 5px
  }
</style>