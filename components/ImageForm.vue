<template>
  <div>
    <b-form>
      <input
        type="file"
        placeholder="ファイルを選択するかドロップしてください"
        mulitple="multiple"
        accept=".jpg, .png, .gif"
        @change="previewImage"
      >

      <img :src="settintImage" alt="" class="img">
      <div class="d-flex flex-row justify-content-end">
        <!-- go to storage -->
        <b-button class="ml-1 mt-2 primary" @click="sendImg">
          送信
        </b-button>
      </div>
      <div>
        <!-- preview image -->
        <b-card
          v-for="(src, index) in previewGifSrcList"
          :key="index"
          class="content-block"
          no-body
        >
          <div>
            <img style="width:370px;" class="img-fluid" :src="src">
          </div>
        </b-card>
      </div>
    </b-form>
  </div>
</template>

<script>

import { storage } from '@/plugins/firebase/firebase'
export default {
  data () {
    return {
      file: '',
      fileName: '',
      uploadedImage: '',
      fileList: '',
      settintImage: '',
      previewGifSrcList: [],
      storageRef: storage.ref()
    }
  },
  methods: {
    previewImage (e) {
      const fileList = e.target.files[0]
      // eslint-disable-next-line no-console
      console.log(fileList)
      this.fileList = fileList
      const fileName = fileList.name
      this.fileName = fileName
      // eslint-disable-next-line no-console
      console.log('this', this.fileList)
      this.createImage(fileList)
    },
    sendImg () {
      const uploadTask = this.storageRef.child(`images/${this.fileName}`).put(this.fileList)
      uploadTask.on('state_changed', (snapshot) => {
        // eslint-disable-next-line no-console
        console.log(snapshot)
      }, (error) => {
        // eslint-disable-next-line no-console
        console.log(error)
      }, () => {
        uploadTask.snapshot.ref.getDownloadURL().then(function (downloadURL) {
          // eslint-disable-next-line no-console
          console.log('downloadURL', downloadURL)
        })
      })
    },
    createImage (fileList) {
      const reader = new FileReader()
      reader.onload = (e) => {
        // eslint-disable-next-line no-console
        console.log('reader', reader.result)
        const settintImage = reader.result
        this.settintImage = settintImage
        // eslint-disable-next-line no-console
        console.log('this.settingImage', this.settintImage)
      }
      reader.readAsDataURL(fileList)
    }
  }
}
</script>

<style scoped>
.img {
  width: 100%;
  height: 499px;
}
</style>
