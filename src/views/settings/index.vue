<template>
  <div class="settings-container">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <!-- 面包屑路径导航 -->
        <el-breadcrumb separator="/">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人设置</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <el-row :gutter="10">
        <el-col :sm="12" :md="15" :lg="12">
          <!-- 表单区域 -->
          <el-form ref="form" :model="userInfo" label-width="80px">
              <el-form-item label="编号">
                {{userInfo.id}}
              </el-form-item>
              <el-form-item label="手机">
                {{userInfo.mobile}}
              </el-form-item>
              <el-form-item label="媒体名称">
                <el-input v-model="userInfo.name"></el-input>
              </el-form-item>
              <el-form-item label="媒体介绍">
                <el-input type="textarea" v-model="userInfo.intro"></el-input>
              </el-form-item>
              <el-form-item label="邮箱">
                <el-input v-model="userInfo.email"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="onSubmit">保存设置</el-button>
              </el-form-item>
          </el-form>
        </el-col>
        <el-col :sm="12" :md="15" :lg="6" :offset="5">
          <label for="file">
            <el-avatar
              shape="square"
              :size="200"
              fit="cover"
              :src="userInfo.photo">
            </el-avatar>
            <p>修改头像</p>
          </label>
          <input
          id="file"
          type="file"
          hidden
          ref="file"
          @change="onFileChange"
          >
        </el-col>
      </el-row>
      </el-card>
      <!-- Dialog 对话框 -->
      <el-dialog
        title="修改头像"
        :visible.sync="dialogAvatarVisible"
        width="30%"
        append-to-body
        @opened="onDialogOpened"
        @closed="onDialogClosed"
      >
        <div class="preview-img-wrap">
          <img
            class="preview-img"
            :src="previewImage"
            ref="preview-img"
            alt=""
          >
        </div>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogAvatarVisible = false">取 消</el-button>
          <el-button type="primary" @click="dialogAvatarVisible = false">确 定</el-button>
        </span>
      </el-dialog>
  </div>
</template>

<script>
import 'cropperjs/dist/cropper.css'
import Cropper from 'cropperjs'
import { getUserProfile } from '@/api/user'
export default {
  name: 'SettingsIndex',
  data () {
    return {
      userInfo: {
        id: null,
        name: '', // 用户名
        email: '',
        mobile: '', // 手机号
        photo: '', // 用户头像
        intro: '' // 头条号简介
      },
      dialogAvatarVisible: false, // 是否显示 Dialog
      previewImage: '', // Dialog 上的预览图片
      cropper: null // 裁剪图片实例对象
    }
  },
  created () {
    this.loadUserInfo()
  },
  methods: {
    onSubmit () {
      console.log('submit!')
    },
    // 获取用户个人资料
    loadUserInfo () {
      getUserProfile().then(res => {
        // console.log(res)
        this.userInfo = res.data.data
      })
    },
    onFileChange () {
      // 获取 上传文件 dom元素
      const file = this.$refs.file
      // console.dir(file)
      // 将其 转换为 blob 这种图片url数据格式
      const blobData = window.URL.createObjectURL(file.files[0])
      // console.log(blobData)
      this.previewImage = blobData

      // 弹出 Dialog 对话框
      this.dialogAvatarVisible = true

      // 处理 点击相同图片 不触发该事件问题
      this.$refs.file.value = ''
    },
    // Dialog 打开动画结束时的回调
    onDialogOpened () {
      const image = this.$refs['preview-img']
      // console.log(image)
      // 判断 当前裁剪实例对象
      if (this.cropper) {
        // 替换成 上传预览图片
        this.cropper.replace(this.previewImage)
        return
      }
      this.cropper = new Cropper(image, {
        aspectRatio: 1,
        dragMode: 'none',
        cropBoxResizable: false,
        viewMode: 1,
        background: false
        // 移动 裁剪框 输出的信息
        // crop (event) {
        //   console.log(event.detail.x)
        //   console.log(event.detail.y)
        //   console.log(event.detail.width)
        //   console.log(event.detail.height)
        //   console.log(event.detail.rotate)
        //   console.log(event.detail.scaleX)
        //   console.log(event.detail.scaleY)
        // }
      })
    },
    // Dialog 关闭动画结束时的回调
    onDialogClosed () {
      // 销毁 裁剪器
      // this.cropper.destroy()
    }
  }
}
</script>

<style scoped lang="less">
.preview-img-wrap {
  .preview-img {
    display: block;

    /* This rule is very important, please don't ignore this */
    max-width: 100%;
    height: 300px;
  }
}
</style>