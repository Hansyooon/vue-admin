<template>
  <div>
    <el-card>
      <el-form inline :model="category">
        <el-form-item label="一级分类">
          <el-select
            v-model="category.category1Id"
            placeholder="请选择"
            @change="getCategory2List"
            :disabled="disabled"
          >
            <el-option
              v-for="category1 in category1List"
              :key="category1.id"
              :label="category1.name"
              :value="category1.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级分类">
          <el-select
            v-model="category.category2Id"
            placeholder="请选择"
            @change="getCategory3List"
            :disabled="disabled"
          >
            <el-option
              v-for="category2 in category2List"
              :key="category2.id"
              :label="category2.name"
              :value="category2.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="三级分类">
          <el-select
            v-model="category.category3Id"
            placeholder="请选择"
            @change="handleGetAttrList"
            :disabled="disabled"
          >
            <el-option
              v-for="category3 in category3List"
              :key="category3.id"
              :label="category3.name"
              :value="category3.id"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  name: "Category",
  props: ["disabled"],
  data() {
    return {
      category: {
        category1Id: "",
        category2Id: "",
        category3Id: "",
      },
      attrList: [],
      category1List: [],
      category2List: [],
      category3List: [],
    };
  },
  methods: {
    async getCategory2List(category1Id) {
      const result = await this.$API.attr.getCategory2(category1Id);
      if (result.code === 200) {
        this.category2List = result.data;
      } else {
        this.$message.error(result.message);
      }
    },
    async getCategory3List(category2Id) {
      const result = await this.$API.attr.getCategory3(category2Id);
      if (result.code === 200) {
        this.category3List = result.data;
      } else {
        this.$message.error(result.message);
      }
    },
    async handleGetAttrList(category3Id) {
      const category = {
        ...this.category,
        category3Id,
      };

      this.$bus.$emit("change", category);
    },
  },
  async mounted() {
    const result = await this.$API.attr.getCategory1();
    if (result.code === 200) {
      this.category1List = result.data;
    } else {
      this.$message.error(result.message);
    }
  },
};
</script>

<style lang="less" scoped>
</style>
