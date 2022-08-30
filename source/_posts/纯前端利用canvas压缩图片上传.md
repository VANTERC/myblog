---
title: 纯前端利用canvas压缩图片上传
date: 2021-02-25 17:57
tags:
---

实现的方式是上传图片file->获取base64->转化为canvas->canvas压缩后转化为base64->base64转化为file
```html
<template>
  <div>
	<input ref="uploadinput" type="file" name="file" accept="image/*" @change="changeImg"/>
  </div>
</template>

<script>
  export default {
    data(){
    	return {
      	picurl:""
      }
    },
    methods: {
      async changeImg(){
              let that = this
              let input = this.$refs.uploadinput
              let file = input.files[0]
              this.picurl = await this.getBase64(file)
        			let newfile = dataURLtoFile(this.picurl,"pic.jpeg")
      },
      //获取Base64
    	getBase64(file) {
        return new Promise((resolve, reject) => {
          const reader = new FileReader();
          reader.readAsDataURL(file);
          reader.onload = () => {
            this.canvasDataURL(reader.result,1500).then(r=>{
              resolve(r);
            }).catch(e=>{
              reject(e);
            })
          }
          reader.onerror = error => reject(error);
        });
      },

      //图片压缩处理
      canvasDataURL(re,w){
        return new Promise((resolve, reject) =>{
          let newImg=new Image();
          newImg.src=re;
          let imgWidth,imgHeight;
          newImg.onload = () =>{
            let img=document.createElement("img");
            img.src=newImg.src;
            imgWidth=img.width;
            imgHeight=img.height;
            let canvas=document.createElement("canvas");
            canvas.width=imgWidth;
            canvas.height=imgHeight;
            let ctx=canvas.getContext("2d");
            ctx.clearRect(0,0,imgWidth,imgHeight);
            ctx.drawImage(img,0,0,imgWidth,imgHeight);
            resolve(canvas.toDataURL("image/jpeg",0.2))
          }
          newImg.onerror = error => reject(error);
        }) 
      },

      //将base64转换为文件
      dataURLtoFile(dataurl, filename) {
        let arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
            bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
        while(n--){
          u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr], filename, {type:mime});
      },
    }
  }
</script>

<style lang="scss" scoped>

</style>





						
```


