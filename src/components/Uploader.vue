<template>
  <div id="root">
    <div class="main-container">
      <div class="image-container">
        <div
          class="click-to-upload"
          v-if="!(imgList.length | pdfList.length)"
          @click="addFilesClick"
        >
          <img class="add-icon" src="../assets/img/add-icon.png" alt />
          <img class="img-icon" src="../assets/img/img-icon.png" alt />
        </div>
        <div class="image-wrap" v-for="(image, index) in imgList" :key="index">
          <img
            class="delete-icon"
            src="../assets/img/delete.png"
            @click="deleteImg(index)"
            alt="delete"
          />
          <img class="portfolio-image" :src="image.file.src" alt />
        </div>
        <div class="pdf-wrap" v-for="(pdf, index) in pdfList" :key="index">
          <img
            class="delete-icon"
            src="../assets/img/delete.png"
            @click="deletePdf(index)"
            alt
          />
          <img class="pdf-icon" src="../assets/img/pdf.png" alt />
          <span class="pdf-name" v-text="pdf.file.name"></span>
        </div>
      </div>

      <input
        @change="fileChange($event)"
        type="file"
        id="upload_file"
        multiple
        style="display: none"
      />

      <div class="img-count">
        <span class="focused-color">{{ imgList.length }}</span>
        files in total
        <span class="focused-color">({{ formattedSize }})</span>
      </div>

      <span
        v-if="imgList.length | pdfList.length"
        class="continue-to-upload"
        @click="addFilesClick"
      >
        <img class="add-icon-small" src="../assets/img/add-icon.png" alt />
      </span>
    </div>
    <button class="btn" id="submit" @click="submitFiles">
      Confirm to Upload
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imgList: [],
      size: 0,
      formattedSize: "0 B",
      pdfList: []
    };
  },

  methods: {
    addFilesClick() {
      document.getElementById("upload_file").click();
    },

    fileChange(event) {
      let files = event.target.files;
      for (let i = 0; i < files.length; i++) {
        this.addFile(files[i]);
      }
    },

    addFile(file) {
      //统计文件 size
      this.size = this.size + file.size;
      this.calculateFileSize(this.size);

      //判断是否为图片文件
      if (file.type.indexOf("image") >= 0) {
        let reader = new FileReader();
        let image = new Image();
        let _this = this;
        reader.readAsDataURL(file);
        reader.onload = function() {
          file.src = this.result;
          image.onload = function() {
            let width = image.width;
            let height = image.height;
            file.width = width;
            file.height = height;
            _this.imgList.push({ file });
          };
          image.src = file.src;
        };
      }

      //2. 如果是 pdf 文件
      if (file.type.indexOf("pdf") >= 0) {
        this.pdfList.push({ file });
      }
    },

    deleteImg(index) {
      //重新统计 file size
      this.size = this.size - this.imgList[index].file.size;
      this.calculateFileSize(this.size);
      this.imgList.splice(index, 1);
    },

    deletePdf(index) {
      //重新统计 file size
      this.size = this.size - this.pdfList[index].file.size;
      this.calculateFileSize(this.size);
      this.pdfList.splice(index, 1);
    },

    // file size 单位转换
    calculateFileSize(bytes) {
      let _this = this;
      if (bytes === 0) _this.formattedSize = "0 B";
      else {
        let k = 1024,
          unit = ["B", "KB", "MB"],
          i = Math.floor(Math.log(bytes) / Math.log(k));
        let result = (bytes / Math.pow(k, i)).toPrecision(3) + " " + unit[i];
        _this.formattedSize = result;
      }
    },

    submitFiles() {
      //...
    }
  }
};
</script>
<style scoped>
#root {
  width: 972px;
  margin: 100px auto;
}
.h1 {
  color: purple;
  opacity: 0.8;
  font-size: 2em;
  width: 100%;
  text-align: center;
  height: 30px;
  line-height: 100px;
}

.main-container {
  border: mediumslateblue 1.5px dashed;
  min-height: 180px;
  border-radius: 10px;
  height: auto;
  width: 972px;
  position: relative;
}

.click-to-upload {
  width: 970px;
  height: 180px;
}

.add-icon {
  width: 25px;
  height: 25px;
  position: relative;
  top: -10px;
}
.img-icon {
  margin-top: 80px;
  width: 46px;
  height: 42px;
  border-radius: 6px;
}

.image-container {
  width: 970px;
  min-height: 180px;
  height: auto;
  display: flex;
  flex-wrap: wrap;
  text-align: center;
  line-height: 80px;
  padding-left: 3px;
}

.image-wrap,
.pdf-wrap {
  width: 135px;
  height: 135px;
  overflow: hidden;
  border: #f3f3f3 dashed 1px;
  position: relative;
  margin: 12px;
}
.pdf-wrap {
  border: #585858 1px solid;
}

.pdf-wrap:hover .delete-icon {
  display: block;
}
.pdf-icon {
  width: 40px;
  height: 45px;
  position: absolute;
  top: 20px;
  left: 35px;
}

.portfolio-image {
  width: auto;
  height: auto;
  vertical-align: middle;
  /* max-width: 100%; */
  max-height: 100%;
}

.delete-icon {
  width: 20px;
  height: 20px;
  position: absolute;
  display: none;
  right: 0;
  top: 0;
}

.image-wrap:hover .delete-icon {
  display: block;
}

.continue-to-upload {
  width: 80px;
  line-height: 60px;
  position: absolute;
  right: 30px;
  text-decoration: underline;
}

.img-count {
  line-height: 60px;
  display: inline;
  padding-left: 30px;
  font-family: "Times New Roman";
  color: #585858;
}
.focused-color {
  color: purple;
}
.add-icon-small {
  width: 30px;
  height: 30px;
  position: absolute;
  right: 30px;
}
.btn {
  margin-top: 30px;
  padding: 8px 10px;
  font-size: 16px;
  display: block;
  color: purple;
  border: purple 1px solid;
  border-radius: 5px;
  font-family: "Times New Roman";
  font-weight: bolder;
  background: lightseagreen;
  position: relative;
  left: 820px;
}
</style>
