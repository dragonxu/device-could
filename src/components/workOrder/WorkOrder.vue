
<template>
    <el-tree :data="treeData" :props="defaultProps" accordion @node-click="handleNodeClick">
    </el-tree>
</template>

 
<script>
export default {
  name: "Test",
  data() {
    return {
      data: [
        { id: 1, parentId: 0, name: "一级菜单A", rank: 1 },
        { id: 2, parentId: 0, name: "一级菜单B", rank: 1 },
        { id: 3, parentId: 0, name: "一级菜单C", rank: 1 },
        { id: 4, parentId: 1, name: "二级菜单A-A", rank: 2 },
        { id: 5, parentId: 1, name: "二级菜单A-B", rank: 2 },
        { id: 6, parentId: 2, name: "二级菜单B-A", rank: 2 },
        { id: 7, parentId: 4, name: "三级菜单A-A-A", rank: 3 },
        { id: 8, parentId: 7, name: "四级菜单A-A-A-A", rank: 4 },
        { id: 9, parentId: 8, name: "五级菜单A-A-A-A-A", rank: 5 },
        { id: 10, parentId: 9, name: "六级菜单A-A-A-A-A-A", rank: 6 },
        { id: 11, parentId: 10, name: "七级菜单A-A-A-A-A-A-A", rank: 7 },
        { id: 12, parentId: 11, name: "八级菜单A-A-A-A-A-A-A-A", rank: 8 },
        { id: 13, parentId: 12, name: "九级菜单A-A-A-A-A-A-A-A-A", rank: 9 },
        { id: 14, parentId: 13, name: "十级菜单A-A-A-A-A-A-A-A-A-A", rank: 10 }
      ],
      defaultProps: {
        children: "children",
        label: "name"
      }
    };
  },
  computed: {
    treeData() {
      let tree = this.data.filter(father => {
        //循环所有项
        let branchArr = this.data.filter(child => {
          return father.id == child.parentId; //返回每一项的子级数组
        });
        if (branchArr.length > 0) {
          father.children = branchArr; //如果存在子级，则给父级添加一个children属性，并赋值
        }
        return father.parentId == 0; //返回第一层
      });
      return tree; //返回树形数据
    }
  },
  methods: {
    handleNodeClick(data) {
      // console.log(data)
      console.log(this.treeData);
    }
  },
  mounted() {
    
  }
};
</script>
 
<style scoped>
</style>

