<template>
  <div id="app">
    <vue-dropzone id="imgDropzone" ref="imgDropzone" :options="dropzoneOptions" @vdropzone-complete="afterComplete"></vue-dropzone>
     <div v-if="images.length >0">
       <div v-for="image in images" :key="image.src">
        <img :src="image.src">
      </div>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase';
import vue2Dropzone from "vue2-dropzone";
import "vue2-dropzone/dist/vue2Dropzone.min.css";
//import HelloWorld from './components/HelloWorld';

let uuid = require("uuid");

export default {
  name: "App",
  components: {
    vueDropzone: vue2Dropzone
  },
  data() {
    return {
      images: [],
      dropzoneOptions: {
        url: "https://httpbin.org/post",
        thumbnailWidth: 250,
        thumbnailHeight: 250,
        addRemoveLinks: false,
        acceptedFiles: ".jpg, .jpeg, .png"
      }
    }
  },
  methods: {
    async afterComplete(file) {
      try {
        const imageName = uuid.v1();
        var metaData = {
          contentType: "image/png"
        }

        const storageRef = firebase.storage().ref();
        const imageRef = storageRef.child(`images/${imageName}.png`)

        await imageRef.put(file, metaData);

        const downloadUrl = await imageRef.getDownloadURL()

        this.images.push({src: downloadUrl});

        this.$refs.imgDropzone.removeFile(file);
      } catch (error) {
        console.log(error);
      }
    }
  }
};
</script>

<style>
  .img-div {
    display: flex;
    margin: 25px;
  }

  img {
    max-width: 250px;
    margin: 15px;
  }
</style>
