### 上传图片至oss 基于element-ui 
```javascript 1.8
<template>
    <div class="dz-upload-photo">
    <el-upload
            :multiple="false"
            class="upload-demo dz-upload-photo"
            :data="formData"
            list-type="picture-card"
            :action=uploadUrl
            :on-preview="handlePreview"
            :before-upload="beforeUpload"
            :on-success="afterUpload"
            :on-erroe="errorUpload"
            :on-remove="removeUpload"
            :on-exceed="exceedLength"
            :file-list="fileUrls"
            :limit="maxCount"
            :disabled="disableFlg" drag>
        <i class="el-icon-plus"></i>
    </el-upload>
    </div>
</template>

<script>
/**
 * 文件上传组件
 * max-count : 最多允许上传多少张
 * tip : 上传提示文字
 * file-list : 上传文件列表
 * size-limit : 单文件大小限制， 单位Kb
 *
 */
export default {
  name: "DzUploadPhoto",
  props: {
    tip: {
      type: String,
      default: "文件大小不超过5M"
    },
    fileList: {
      type: Array,
      default: () => {
        return [];
      }
    },
      idex: {
          type: Number | String,
          default: () => {
              return -1;
          }
      },
    sizeLimit: {
      type: Number | String,
      default: 1024
    },
    maxCount: {
      type: Number,
      default: 1
    },
    context: {
      default: "document"
    }
  },
  data() {
    return {
      uploadUrl: "",
      formData: {
        key: "",
        Filename: ""
      },
      fileUrls: [],
      // 上传图片--阿里云服务器 异步返回url(有延迟)
      uploadPics: [],
      serverHost: "",
      disableFlg: false
    };
  },
  created() {
    this.fileUrls = this.fileList;
    this.uploadPics = this.fileList;
    this.getFormData();
  },
  mounted() {
    if (this.fileUrls.length >= this.maxCount) {
      document
        .getElementsByClassName("dz-upload-photo")[0]
        .getElementsByClassName("el-upload ")[0].style.display = "none";
    }
  },
  methods: {
    async getFormData() {
      let getToken = await this.$axios.post(
        process.env.VUE_APP_BASE_URL + "/oss/get-token",
        { context: this.context }
      );
      if (getToken.status == 200) {
        try {
          getToken.data = JSON.parse(getToken.data);
        } catch (error) {
          getToken.data = {};
        }
        if (getToken.data.code == 0) {
          let tokenObj = getToken.data.content;
          this.uploadUrl = tokenObj["host"] || "";
          let tmpFileRandomTxt = this.context + "/" + this.getRandomString();
          /**
           * 上传阿里云必不可少的参数
           */
          this.formData = {
            key: tmpFileRandomTxt,
            Filename: tmpFileRandomTxt,
            policy: tokenObj["policy"] || "",
            OSSAccessKeyId: tokenObj["accessid"] || "",
            success_action_status: "200",
            callback: tokenObj["callback"] || "",
            signature: tokenObj["signature"] || ""
          };
          this.serverHost = tokenObj["host"];
          this.disableFlg = false;
        }
      } else {
        this.dz.showMessage("error", getToken.data.msg);
      }
    },
    handlePreview: function(file) {
      window.open(file.url);
    },
    /**
     * 上传前的回调函数
     */
    beforeUpload: function(file) {
      // 上传大小限制
      if (file.size / 1024 > this.sizeLimit) {
        this.dz.showMessage("error", `超过限制大小${this.sizeLimit / 1024}M`);
        return false;
      }
      // 上传格式限制
      let imgType = ["image/jpg", "image/jpeg", "image/png", "image/gif"];
      if (imgType.indexOf(file.type) === -1) {
        this.dz.showMessage("error", "请上传图片");
        return false;
      }
      // 文件后缀
      let filePostfix = "";
      if (file.name.indexOf(".") > -1) {
        let fileTxt = file.name.split(".");
        filePostfix = fileTxt[fileTxt.length - 1];
      }
      this.formData.key = this.formData.key + "." + filePostfix;
      this.formData.Filename = this.formData.Filename + "." + filePostfix;
      this.disableFlg = true;
      return this.getFormData(file);
    },
    /**
     * 上传成功的回调函数
     */
    afterUpload: function(response, file, fileList) {
      // let newFileList = []
      // _.forOwn(fileList, function (item, key) {
      //     newFileList.push({
      //         name: item.name,
      //         url: item.url,
      //         size: item.size
      //     })
      // })
      let listLen = fileList.length;
      let fileItem = fileList[listLen - 1];
      // 每次只取最后一个数据
      this.uploadPics.push({
        name: fileItem.name,
        url: this.serverHost + "/" + this.formData.key,
        size: fileItem.size
      });
      if (fileList.length >= this.maxCount) {
        document
          .getElementsByClassName("dz-upload-photo")[0]
          .getElementsByClassName("el-upload ")[0].style.display = "none";
      }
      if (this.idex > -1) {
          let data = {
              result: this.uploadPics,
              index: this.idex
          }
          this.$emit("updatefileList",data);
      }  else {
          this.$emit("updatefileList", this.uploadPics);
      }
    },
    errorUpload(err, file, fileList) {
      console.log(err, file, fileList);
    },
    /**
     * 删除的回调函数
     */
    removeUpload(file, fileList) {
      this.uploadPics = fileList;
      this.fileUrls = fileList;
      document
        .getElementsByClassName("dz-upload-photo")[0]
        .getElementsByClassName("el-upload ")[0].style.display = "inline-block";
        if (this.idex > -1) {
            let data = {
                result: this.uploadPics,
                index: this.idex
            }
            this.$emit("updatefileList",data);
        }  else {
            this.$emit("updatefileList", this.uploadPics);
        }
    },
    /**
     * 超过限制大小的回调函数
     */
    exceedLength: function(file, fileList) {
      this.dz.showMessage("error", `超出上传个数${this.maxCount}个`);
    },
    /**
     * 生成随机字符串
     * @param len
     * @returns {string}
     */
    getRandomString: function(len) {
      let leng = len || 32;
      let chars = "ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678";
      let maxPos = chars.length;
      let pwd = "";
      for (let i = 0; i < leng; i++) {
        pwd += chars.charAt(Math.floor(Math.random() * maxPos));
      }
      return pwd;
    }
  }
};
</script>

<style lang="less">
.dz-upload-photo {
    .dz-upload-photo .el-upload.el-upload--picture-card{
        width: 148px;
        height: 148px;
    }
    .dz-upload-photo .el-upload.el-upload--picture-card i{
        line-height: 128px;
        color: #8c939d;
        font-size: 30px;
    }
    .el-upload {
    width: auto;
    height: auto;
    -webkit-transition: all 0.6s;
    -moz-transition: all 0.6s;
    -ms-transition: all 0.6s;
    -o-transition: all 0.6s;
    transition: all 0.6s;
  }
  .el-upload-dragger {
    width: 155px !important;
    height: 140px !important;
    border-radius: 10px;
    &:hover {
      border-color: #0096e4;
    }
  }
  .el-upload-list--picture-card .el-upload-list__item {
    width: 155px;
    height: 140px;
    border-radius: 10px;
    box-sizing: border-box;
    margin: 0 30px 8px 0;
  }
  .el-upload--picture-card:hover,
  .el-upload:focus {
    border-color: #0096e4;
    color: #0096e4;
  }
}
</style>
```