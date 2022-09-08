---
title: Ant Design VUE Upload组件 OSS上传图片
date: 2021-03-12 20:52
tags:
---


![image.png](https://vanterc.oss-cn-beijing.aliyuncs.com/blog/1585566760218-eb503e89-dfbc-4fc9-a81c-97bb01aac7be.png)
之前写图片上传都是通过自定义方法将file和OSS信息组装到formData里面然后设置请求头'Content-Type': 'multipart/form-data'来上传的，现在直接用自带的action上传还是挺方便的。

<!-- more -->
```js
<template>
	<a-upload
    :action="urlhost"
    listType="picture-card"
    accept="image/*"
    @preview="handlePreview"
    @change="handleChange"
    :remove="removeImg"
    :beforeUpload="beforeUpload"
    :data="paramsData">
    <a-icon type="plus" />
      <div class="ant-upload-text">点击上传</div>
 </a-upload>
   <a-modal :visible="previewVisible" :footer="null" @cancel="handleCancel">
      <img alt="example" style="width: 100%" :src="previewImage" />
   </a-modal>
</template>


<script>
  function getBase64(file) {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = () => resolve(reader.result);
      reader.onerror = error => reject(error);
    });
  }
  import {getOssSignWeb} from '../../../api/upload.js'; //获取后端OSS信息接口
  export default {
  	return {
    	businessId:'',//业务id
    	previewVisible:false,
      previewImage: '',
    	paramsData:{ //OSS信息对象 也是用来需要上传的参数
          'OSSAccessKeyId':'',
          'policy':'',
          'Signature':'',
          'key':'',
          'success_action_status':200
        },
    	urlhost:'', //上传路径
  	},
    methods: {
    	async handlePreview(file) { //点击图片预览
        if (!file.url && !file.preview) {
          file.preview = await getBase64(file.originFileObj);
        }
        this.previewImage = file.url || file.preview;
        this.previewVisible = true;
      },
      beforeUpload(file, fileList){ //在上传前获取OSS信息
        return new Promise((resolve,reject)=>{
          let params ={
            "businessId":this.businessId,
            "suffix":'.jpg'
          }
          getOssSignWeb(params).then(res=>{
            	this.urlhost = res.retData.host
              this.paramsData = { //组装参数
                'OSSAccessKeyId':res.retData.accessid,
                'policy':res.retData.policy,
                'Signature':res.retData.signature,
                'key':res.retData.dir+res.retData.fileName,
                'success_action_status':200
              }
              resolve(file)
            }).catch(e=>{
              reject()
            })
        })
      },
      removeImg(){
        return new Promise((resolve,reject)=>{
          this.$confirm({
            title: '提示',
            content: '是否删除这张图片',
            okText: '确定',
            okType: 'danger',
            cancelText: '取消',
            onOk() {
              resolve(true)
            },
            onCancel() {
              resolve(false)
            },
          })
        })
      },
      handleChange(info) {
        if (info.file.status === 'done') {
          this.$message.success('上传成功！');
        }
        if (info.file.status === 'error') {
          this.$message.error('上传失败！');
        }
      },
      handleCancel() {
        this.previewVisible = false;
      },
    }
  }
</script>
```


