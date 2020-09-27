<template>
  <el-form>
    <el-form-item label="SPU名称" label-width="100px">
      <el-input v-model="spuInfo.spuName" placeholder="SPU名称"></el-input>
    </el-form-item>

    <!-- 
    category3Id: 61
    description: "小米手机就是好,每天都是起得早,没事还要叫大嫂"
    id: 1504
    spuImageList: null
    spuName: "小米Pro110"
    spuSaleAttrList: [{id: 4950, spuId: 1504, baseSaleAttrId: 1, saleAttrName: "选择颜色",…},…]
    tmId: 247 
    -->
    <el-form-item label="品牌" label-width="100px">
      <!-- value必须写 -->
      <!-- select在收集数据的时候，收集的就是option中选中的那个的id -->
      <el-select placeholder="请选择品牌" v-model="spuInfo.tmId">
        <el-option
          :label="tm.tmName"
          :value="tm.id"
          v-for="(tm, index) in trademarkList"
          :key="tm.id"
        >
        </el-option>
      </el-select>
    </el-form-item>

    <el-form-item label="SPU描述" label-width="100px">
      <el-input v-model="spuInfo.description" placeholder="SPU描述" type="textarea" rows="4"></el-input>
    </el-form-item>

    <el-form-item label="SPU图片" label-width="100px">
      <el-upload
        action="https://jsonplaceholder.typicode.com/posts/"
        list-type="picture-card"
        :on-preview="handlePictureCardPreview"
        :on-remove="handleRemove"
        :file-list="spuImageList"
      >
        <i class="el-icon-plus"></i>
      </el-upload>
      <el-dialog :visible.sync="dialogVisible">
        <img width="100%" :src="dialogImageUrl" alt="" />
      </el-dialog>
    </el-form-item>

    <el-form-item label="销售属性" label-width="100px">
      <el-select placeholder="还有3没选择" value="">
        <el-option value=""> </el-option>
      </el-select>
      <el-button type="primary" icon="el-icon-plus">添加销售属性</el-button>
      <el-table border style="width: 100%; margin-top: 20px">
        <el-table-column align="center" type="index" label="序号" width="80">
        </el-table-column>
        <el-table-column prop="prop" label="属性名" width="150">
        </el-table-column>
        <el-table-column prop="prop" label="属性值名称列表" width="width">
        </el-table-column>
        <el-table-column label="操作" width="150"> </el-table-column>
      </el-table>
    </el-form-item>

    <el-form-item label-width="100px">
      <el-button type="primary">保存</el-button>
      <el-button @click="$emit('update:visible', false)">取消</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  name: "SpuForm",
  props: ["visible"],
  data() {
    return {
      dialogImageUrl: "",
      dialogVisible: false,
      spuInfo: {
        spuName: "",
        tmId: "",
        description:''
      },
      spuImageList: [],
      trademarkList: [],
      baseSaleAttrList: [],
    };
  },
  methods: {
    // 添加的请求
    async initAddSpuFormData() {
      // 通过点击父组件当中的添加spu按钮，父组件当中会调用这个函数
      // http://localhost:9528/dev-api/admin/product/baseTrademark/getTrademarkList
      const trademarkListResult = this.$API.trademark.getList(); //获取所有的品牌列表
      if (trademarkListResult.code === 200) {
        this.trademarkList = trademarkListResult.data;
      }
      // http://localhost:9528/dev-api/admin/product/baseSaleAttrList
      const baseSaleAttrListResult = this.$API.spu.getSaleList(); //根据SKU的id查询SKU的详细信息
      if (baseSaleAttrListResult.code === 200) {
        this.baseSaleAttrList = baseSaleAttrListResult.data;
      }
    },
    // 修改的请求
    async initUpdateSpuFormData(row) {
      // console.log(row);
      // 通过点击父组件当中的修改spu按钮，父组件当中会调用这个函数
      // http://localhost:9528/dev-api/admin/product/getSpuById/1509
      const result = await this.$API.spu.get(row.id); //根据SKU的id查询SKU的详细信息
      if (result.code === 200) {
        this.spuInfo = result.data;
      }

      // http://localhost:9528/dev-api/admin/product/spuImageList/1509
      const ImageListResult =await this.$API.sku.getSpuImageList(row.id); //  获取指定SPU的id对应的图片列表
      // 先把spuImageList定义了变量接收数据， 
      // ImageListResult.data这个数组本身就是响应式数据
      let spuImageList = ImageListResult.data
      
      //拿到数据之后第一件事，在所有的图片对象数据当中,遍历添加name和URL属性,全部添加name和url为了upload可以展示照片墙
      spuImageList.forEach(item => {
        item.name = item.imgName;
        item.url = item.imgUrl;
      });
      if (ImageListResult.code === 200) {
        this.spuImageList = spuImageList;
      }
      // http://localhost:9528/dev-api/admin/product/baseTrademark/getTrademarkList
      const trademarkListResult = await this.$API.trademark.getList(); //获取所有的品牌列表
      if (trademarkListResult.code === 200) {
        this.trademarkList = trademarkListResult.data;
      }
      // http://localhost:9528/dev-api/admin/product/baseSaleAttrList
      const baseSaleAttrListResult = await this.$API.spu.getSaleList(); //根据SKU的id查询SKU的详细信息
      if (baseSaleAttrListResult.code === 200) {
        this.baseSaleAttrList = baseSaleAttrListResult.data;
      }
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
  },
};
</script>


