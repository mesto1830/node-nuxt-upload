<template>
  <div>
    <form @submit.prevent="sendFile" enctype="multipart/form-data">
      <h3>Upload Single File</h3>
      <span v-if="message" class="success">{{message}}</span>
      <span v-if="error" class="danger">{{error}}</span>
      <div class="field">
        <div class="file is-boxed is-primary">
          <label for="file" class="file-label">
            <input type="file" class="file-input" multiple ref="file" id="file" accept="image/*" @change="selectFile">
            <span class="file-cta">
              <span class="file-icon">
                <i class="fas fa-upload"></i>
              </span>
              <span class="file-label">
                Please choose a file...
              </span>
            </span>
            <span v-if="file.name" class="file-name">{{file.name}}</span>
          </label>
        </div>
        </div>
      <div class="field">
        <button class="button is-info">Send</button>
      </div>
    </form>
  </div>
</template>

<script>
import axios from 'axios'
export default{
  name:'Upload',
  data () {
    return {
      file: '',
      message:'',
      error: ''
    }
  },
  mounted(){

  },
  methods:{
    async selectFile(){
      this.file = this.$refs.file.files[0]
      this.message = ''
      this.error = ''
    },
    async sendFile(){
      try {
        if(this.file){
          const formData = new FormData()
          formData.append('file', this.file)
          await axios.post('/api/upload', formData).then(result => {
            this.message = result.data.message
            this.file = ''
            this.error = result.data.error
            console.log(result.data.error)
          })
        } else {
          this.error = 'Please choose an image!'
        }
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>