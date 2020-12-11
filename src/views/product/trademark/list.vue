<template>
  <div>
    <!-- 引入添加按钮 -->
    <el-button type="primary" icon="el-icon-plus" @click="dialogVisible = true"
      >添加</el-button
    >
    <!-- 引入表格 -->
    <el-table :data="tradeMark" border style="width: 100%; margin: 20px 0">
      <el-table-column type="index" label="序号" width="80" align="center">
      </el-table-column>
      <el-table-column prop="tmName" label="品牌名称"> </el-table-column>
      <el-table-column prop="logoUrl" label="品牌LOGO">
        <template slot-scope="scope">
          <img class="trademark_logo" :src="scope.row.logoUrl" alt="logo" />
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <el-button type="warning" icon="el-icon-edit">修改</el-button>
        <el-button type="danger" icon="el-icon-delete">删除</el-button>
      </el-table-column>
    </el-table>
    <!-- 分页器 -->
    <el-pagination
      class="trademark-pagination"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page.sync="page"
      :page-sizes="[3, 6, 9]"
      :page-size.sync="limit"
      layout="prev, pager, next, jumper, sizes, total"
      :total="total"
    >
    </el-pagination>
    <!-- 添加品牌弹框 -->
    <el-dialog title="添加品牌" :visible.sync="dialogVisible" width="50%">
      <el-form
        :model="addTradeMark"
        :rules="rules"
        ref="addTradeMark"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="品牌名称" prop="tmName">
          <el-input v-model="addTradeMark.tmName"></el-input>
        </el-form-item>
        <el-form-item label="活动名称" prop="logoUrl">
          <el-upload
            class="avatar-uploader"
            :action="`${$BASE_API}/admin/product/fileUpload`"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
          >
            <img
              v-if="addTradeMark.logoUrl"
              :src="addTradeMark.logoUrl"
              class="avatar"
            />
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitForm('addTradeMark')"
          >确 定</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "TrademarkList",
  data() {
    return {
      tradeMark: [], //所有数据
      total: 0,
      page: 1,
      limit: 3,
      dialogVisible: false,
      addTradeMark: {
        tmName: "",
        logoUrl: "",
      },
      rules: {
        tmName: [
          { required: true, message: "请输入品牌名称", trigger: "blur" },
        ],
        logoUrl: [
          { required: true, message: "请上传品牌LOGO", trigger: "change" },
        ],
      },
    };
  },
  methods: {
    submitForm(addTradeMark) {
      this.$refs[addTradeMark].validate(async (valid) => {
        if (valid) {
          const result = await this.$API.trademark.addTrademark(this.addTradeMark);
          if (result.code === 200) {
            this.$message.success("添加品牌数据成功");
            this.dialogVisible = false;
            this.getPageList(this.page, this.limit);
          } else {
            this.$message.error(result.message);
          }
        }
      });
    },
    handleAvatarSuccess(file) {
      this.addTradeMark.logoUrl = file.data;
    },
    beforeAvatarUpload(file) {
      const imgType = ["image/jpeg", "image/jpg", "image/png"];
      const isType = imgType.indexOf(file.type) > -1;
      const isLt = file.size / 1024 < 50;

      if (!isType) {
        this.$message.error("上传品牌图片只能是 JPG、PNG 格式!");
      }
      if (!isLt) {
        this.$message.error("上传品牌图片大小不能超过 50kb!");
      }
      return isType && isLt;
    },
    handleSizeChange(limit) {
      this.getPageList(this.page, limit);
    },
    handleCurrentChange(page) {
      this.getPageList(page, this.limit);
    },

    async getPageList(page = 1, limit = 3) {
      const result = await this.$API.trademark.getPageList(page, limit);
      if (result.code === 200) {
        this.$message.success("获取品牌分页列表成功");
        this.tradeMark = result.data.records;
        this.total = result.data.total;
        this.page = result.data.current;
        this.limit = result.data.size;
      } else {
        this.$message.success("获取品牌分页列表失败");
      }
    },
  },
  mounted() {
    this.getPageList(this.page, this.limit);
  },
};
</script>
<style lang="sass" scoped>
.trademark_logo
  width: 150px
.trademark-pagination
  text-align: right
/deep/.el-pagination__sizes
  margin-left: 250px
/deep/.avatar-uploader .el-upload
  border: 1px dashed #d9d9d9
  border-radius: 6px
  cursor: pointer
  position: relative
  overflow: hidden

/deep/.avatar-uploader .el-upload:hover
  border-color: #409EFF

/deep/.avatar-uploader-icon
  font-size: 28px
  color: #8c939d
  width: 178px
  height: 178px
  line-height: 178px
  text-align: center

/deep/.avatar
  width: 178px
  height: 178px
  display: block
</style>
