<template>
  <!-- 三级分类可操作性选择 -->
  <el-form :inline="true" :model="cform" :disabled="!isShowList">
    <el-form-item label="一级分类">
      <!-- select v-model收集的是选中的某一项的value值 -->
      <el-select v-model="cform.category1Id" placeholder="请选择" @change="handlerCategory1">
        <el-option :label="c1.name" :value="c1.id" v-for="(c1,index) in category1List" :key="c1.id"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="二级分类">
      <!-- select v-model收集的是选中的某一项的value值 -->
      <el-select v-model="cform.category2Id" placeholder="请选择" @change="handlerCategory2">
        <el-option :label="c2.name" :value="c2.id" v-for="(c2,index) in category2List" :key="c2.id"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="三级分类">
      <!-- select v-model收集的是选中的某一项的value值 -->
      <el-select v-model="cform.category3Id" placeholder="请选择" @change="handlerCategory3">
        <el-option :label="c3.name" :value="c3.id" v-for="(c3,index) in category3List" :key="c3.id"></el-option>
      </el-select>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  name: "CategorySelector",
  props:['isShowList'],
  data() {
    return {
      cform: {
        category1Id: "",
        category2Id: "",
        category3Id: "",
      },
      category1List: [],
      category2List: [],
      category3List: [],
    };
  },
  mounted() {
    this.getCategory1List();
  },
  methods: {
    //获取一级分类列表
    async getCategory1List() {
      const result = await this.$API.category.getCategorys1();
      if (result.code === 200) {
        this.category1List = result.data;
      }
    },

    //选中1级分类的时候获取二级分类列表
    async handlerCategory1(category1Id){
      //重新选择1级分类的时候要把23级清空
      this.cform.category2Id = ''
      this.cform.category3Id = ''
      this.category2List = []
      this.category3List = []

      this.$emit('handlerCategory',{categoryId:category1Id,level:1})

      const result = await this.$API.category.getCategorys2(category1Id);
      if (result.code === 200) {
        this.category2List = result.data;
      }
    },

    //选中2级分类的时候获取三级分类列表
    async handlerCategory2(category2Id){
      this.cform.category3Id = ''
      this.category3List = []

      this.$emit('handlerCategory',{categoryId:category2Id,level:2})

      const result = await this.$API.category.getCategorys3(category2Id);
      if (result.code === 200) {
        this.category3List = result.data;
      }
    },


    //选中3级分类的时候要把id传给父组件
    handlerCategory3(category3Id){
      this.$emit('handlerCategory',{categoryId:category3Id,level:3})
    }

  },
};
</script>
