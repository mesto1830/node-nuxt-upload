<template>
  <div>
    <form @submit.prevent="sendFile" enctype="multipart/form-data">
      <h3>Upload Multiple File</h3>
      <span v-if="message" class="success">{{message}}</span>
      <span v-if="error" class="danger">{{error}}</span>
      <div class="dropzone">
        <input type="file" class="input-field" multiple ref="file" @change="sendFile">
        <p v-if="!uploading" class="call-to-action">Drag your files here...</p>
        <p class="progress-con" v-else>
          <progress  class="progress is-link" :value="progress" max="100"></progress>
          <span>{{progress}}%</span>
        </p>
      </div>

      <div class="content">
        <ul>
          <li v-for="file in uploadedFile[0]" :key="file.originalname">
            {{file.originalname}}
          </li>
        </ul>
      </div>
    </form>
  </div>
</template>

<script>
import axios from 'axios'
export default{
  name:'Dropzone',
  data () {
    return {
      message:'',
      error: '',
      uploading: false,
      uploadedFile: [],
      progress: 0
    }
  },
  mounted(){

  },
  methods:{
    async sendFile(){
      const formData = new FormData()
      const files = this.$refs.file.files
      try {
        if(files.length > 2){
          this.error = 'File length must be less 3'
        } else {
          this.error = ''
          for (const item of files) {
            formData.append('files', item)
          }
          this.uploading = true
          const res = await axios.post('/api/uploadmulti', formData, {
            onUploadProgress: e => this.progress = Math.round(e.loaded * 100 / e.total)
          })
  
          this.uploadedFile.push(res.data.files)
          this.uploading = false
        }

      } catch (error) {
        console.error(error)
        this.uploading = false
      }
    }
  }
}
</script>

<style scoped>
  .dropzone {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 200px;
    padding: 10px;
    position: relative;
    cursor: pointer;
    outline: 2px dotted #ccc;
    outline-offset: -10px;
  }
  .input-field{
    width: 100%;
    height: 200px;
    padding: 10px;
    position: absolute;
    opacity: 0;
    cursor: pointer;
  }
  .dropzone .call-to-action{
    font-size: 30px;
  }
  .progress-con{
    display: flex;
    flex-direction: column;
    align-items: center;
    width:90%;
  }
  .progress{
    margin: 5px auto;
  }
  ul{
    list-style: none;
  }
</style>