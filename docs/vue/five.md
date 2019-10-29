## oss 上传
```JAVASCRIPT 1.8
<template lang="html">
  <div class="pc-upload">
    <el-upload ref="upload" :data="formData" list-type="picture-card"  class="avatar-uploader" :file-list="fileLists" :show-file-list="false" :multiple="multiple" :action="uploadUrl" :before-upload="beforeUpload" :on-error="uploadError" :on-success="afterUpload" ><i class="el-icon-plus" style="width: 100%" v-loading="loading"></i></el-upload>
  </div>
</template>

<script>
import axios from 'axios';
const config = {
  headers: { 'Content-Type': 'application/json' },
  data: {
  }
};
axios.create(config);
export default {
  name: 'pc-upload',
  props: {
    tips: {},
    fileList: {},
    sizeLimit: {},
    imageUrl: {
      type: String,
      default: 'http://***.com/oss/get-token'
    },
    context: {
      default: 'file'
    },
    multiple: {
      default: false
    },
    list: {
      type: Object,
      default: () => {}
    },
    fileFormat: {
      type: Array,
      default: () => [
        '.png',
        '.jpg',
        '.gif'
      ]
    }
  },
  data: function () {
    return {
      iptHtml: '',
      isImg: false,
      isVideo: false,
      uploadUrl: '',
      formData: {},
      fileUrl: '',
      ossContext: 'photo',
      loading: false,
      fileName: '',
      fileLists: [],
      UpLoadFilesLength: 0,
      num: 0,
      // 单次上传的文件数
      singleFileList: []
    };
  },
  methods: {
    beforeUpload: function (file) {
      this.ossContext = this.context;
      if (file.size / 1024 / 1024 > this.sizeLimit) {
        this.$message.error(this.tips);
        return false;
      }

      const fileType = this.fileType(file.name).toLowerCase();
      const isType = this.inArray(fileType, this.fileFormat);
      if (!isType) {
        this.$message.error('文件类型错误');
        return false;
      }
      this.num++;
      this.loading = true;
      const _this = this;
      this.fileType = fileType;
      return _this.getFormData(fileType, file);
    },
    async getFormData(fileType) {
      const component = this;
      const data = await axios.post(this.imageUrl, { context: this.ossContext });
      const tokenObj = data.data.content;
      component.uploadUrl = tokenObj.host || '';
      component.fileName = this.getRandomString() + fileType;
      component.formData = {
        key: component.ossContext + '/' + component.fileName,
        policy: tokenObj.policy || '',
        OSSAccessKeyId: tokenObj.accessid || '',
        success_action_status: 200,
        callback: tokenObj.callback || '',
        signature: tokenObj.signature || '',
      };
      // component.file = {
      //   url: tokenObj['host'] + '/' + component.formData.key,
      //   name: dz.utils.fileName(file.name),
      //   type: fileType
      // }
    },
    afterUpload(response, file) {
      let newFile = '';
      newFile = {
        name: this.ToFileName(file.name),
        url: this.uploadUrl + '/' + response.object,
        size: file.size,
        type: response.mimeType,
        uid: file.uid,
        allName: file.name
      };
      this.singleFileList.push(newFile);
      this.$emit('update:list', newFile);
      if (this.singleFileList.length === this.num) {
        this.loading = false;
      } else {
        this.loading = true;
      }
      if (this.$refs.upload.uploadFiles) {
        const length = this.$refs.upload.uploadFiles.length;
        // 全局变量，用来计算upLoadSuccess方法调用次数
        this.UpLoadFilesLength++;
        if (this.UpLoadFilesLength === length) {
          // 如果方法调用的次数和文件列表的长度相同，说明所有文件都上传完毕，将该全局变量置0
          this.UpLoadFilesLength = 0;
          this.$refs.upload.uploadFiles = [];
        }
      }
    },
    uploadError() {
      this.loading = false;
      if (this.$refs.upload.uploadFiles) { // 如果存在这个值
        const filesList = this.$refs.upload.uploadFiles.slice();
        // 错误信息拼接变量
        let UpLoadAllErrorStr = '';
        for (let i = 0; i < filesList.length; i++) {
          if (filesList[i].response) {
            if (filesList[i].response.status !== 'ok') { // 如果该文件上传后返回的状态值不是200，则说明该文件上传错误
              UpLoadAllErrorStr += `${filesList[i].response.message}<br/>`;
            }
          }
        }
        if (UpLoadAllErrorStr) {
          this.$message({ type: 'error', message: UpLoadAllErrorStr });
          this.$refs.upload.uploadFiles = [];
          return false;
        }
      }
      this.$message({ type: 'error', message: '上传文件失败！' });
      this.$refs.upload.uploadFiles = [];
      return true;
    },
    /**
     * 返回文件类型
     */
    fileType: function (filename) {
      if (filename === '' || filename === null) {
        alert('文件名称错误');
      }
      const index1 = filename.lastIndexOf('.');
      const index2 = filename.length;
      // 后缀名
      const postf = filename.substring(index1, index2);
      return postf;
    },
    /**
     *
     * @param item
     * @param array
     */
    inArray: function (item, array) {
      let isTrue = false;
      array.forEach(function (val) {
        if (item === val) {
          isTrue = true;
        }
      });
      return isTrue;
    },
    /**
     * 生成随机字符串
     * @param len
     * @returns {string}
     */
    getRandomString: function (len) {
      const leng = len || 32;
      const chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678';
      const maxPos = chars.length;
      let pwd = '';
      for (let i = 0; i < leng; i++) {
        pwd += chars.charAt(Math.floor(Math.random() * maxPos));
      }
      return pwd;
    },
    ToFileName: function (filename) {
      if (filename === '' || filename === null) {
        alert('文件名称错误');
      }
      const index1 = filename.lastIndexOf('.');
      const index2 = filename.length;
      const postf = filename.substring(index1, -index2);// 后缀名
      return postf;
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../style/global.css";

</style>
```