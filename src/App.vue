<script setup lang="ts">
import { ref, reactive } from "vue";
import { VueCropper } from "vue-cropper";
import "vue-cropper/dist/index.css";

interface previewsType {
  w: number;
  h: number;
  div: string;
  img: string;
  url: string;
}

const cropper = ref();
const crap = ref(false);
const previews = reactive<previewsType>({
  w: 0,
  h: 0,
  div: "",
  img: "",
  url: "",
});
const option = reactive({
  img: "https://dorlk2ndanptu.cloudfront.net/comfyui-server/file/9445efcefbe56b3611d30c4b2088be8314134b6d292ad77d63db8508e266aaea.jpg?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9kb3JsazJuZGFucHR1LmNsb3VkZnJvbnQubmV0L2NvbWZ5dWktc2VydmVyL2ZpbGUvOTQ0NWVmY2VmYmU1NmIzNjExZDMwYzRiMjA4OGJlODMxNDEzNGI2ZDI5MmFkNzdkNjNkYjg1MDhlMjY2YWFlYS5qcGciLCJDb25kaXRpb24iOnsiRGF0ZUxlc3NUaGFuIjp7IkFXUzpFcG9jaFRpbWUiOjE3Mjk4MDU3MDd9fX1dfQ%3D%3D&Signature=reAwGwoyUFwMu36pfpTy1lqQdR%2BnYa6k6vOCi0ySAZURAezY6ZmfoNSflBEuQbBtvWKslIHrcP1b3gD0Q0kcqbC3wBrrM7dottarNoSH5llPc3Iir5qR8FUzr5M2HRGJXgzdSMefaahOBxdxUbenhQB2x%2Bx7u403u0lRzBGx5mHYugVRe1UyR1pE%2BakZB6Gm4R%2FJLU4Ac5BjhwjKR3On1eCwGOIGP%2FWbn9y8jWRZpktFSXDnM1ZZS8N%2F%2BbbziuQOnujiZA8MsK218mgXYOoVbnmX%2BOy3SrLHcYZdm3w8WLXM8uTWgkK4k7C3EfjCLaw978uzAcQVbvdz2D0%2Fze5x2Q%3D%3D&Key-Pair-Id=K1OQGW4C1HG0ME",
  size: 1,
  full: false,
  outputType: "png",
  canMove: true,
  fixedBox: false,
  original: false,
  canMoveBox: true,
  autoCrop: true,
  autoCropWidth: 750,
  autoCropHeight: 340,
  centerBox: true,
  high: true,
  max: 99999,
});

const startCrop = () => {
  crap.value = true;
  cropper.value.startCrop();
};

const stopCrop = () => {
  crap.value = false;
  cropper.value.stopCrop();
};

const clearCrop = () => {
  cropper.value.clearCrop();
};

const changeScale = (num: number) => {
  cropper.value.changeScale(num);
};

const rotateLeft = () => {
  cropper.value.rotateLeft();
};

const rotateRight = () => {
  cropper.value.rotateRight();
};

const finish = (type: string) => {
  if (type === "blob") {
    cropper.value.getCropBlob((data: Blob) => {
      const img = URL.createObjectURL(data);
      console.log(img);
    });
  } else {
    cropper.value.getCropData((data: string) => {
      console.log(data);
    });
  }
};

const realTime = (data: any) => {
  Object.assign(previews, data);
};

const uploadImg = (e: Event) => {
  const file = (e.target as HTMLInputElement).files?.[0];
  if (file) {
    if (!/\.(gif|jpg|jpeg|png|bmp|GIF|JPG|PNG)$/.test(file.name)) {
      alert("图片类型必须是.gif,jpeg,jpg,png,bmp中的一种");
      return;
    }
    const reader = new FileReader();
    reader.onload = (e) => {
      const data = e.target?.result;
      option.img = data as string;
    };
    reader.readAsDataURL(file);
  }
};

const imgLoad = (msg: any) => {
  console.log(msg);
};

const cropMoving = (data: any) => {
  console.log(data, "截图框当前坐标");
};
</script>

<template>
  <div class="container">
    <div class="cropper-container">
      <VueCropper
        ref="cropper"
        :img="option.img"
        :outputSize="option.size"
        :outputType="option.outputType"
        :info="true"
        :full="option.full"
        :canMove="option.canMove"
        :canMoveBox="option.canMoveBox"
        :original="option.original"
        :autoCrop="option.autoCrop"
        :autoCropWidth="option.autoCropWidth"
        :autoCropHeight="option.autoCropHeight"
        :fixedBox="option.fixedBox"
        :centerBox="option.centerBox"
        :high="option.high"
        :maxImgSize="option.max"
        @realTime="realTime"
        @imgLoad="imgLoad"
        @cropMoving="cropMoving"
      />
    </div>
  </div>
  <div class="button-container">
    <button @click="startCrop" class="btn">开始</button>
    <button @click="stopCrop" class="btn">停止</button>
    <button @click="clearCrop" class="btn">清除</button>
    <button @click="changeScale(1)" class="btn">放大</button>
    <button @click="changeScale(-1)" class="btn">缩小</button>
    <button @click="rotateLeft" class="btn">向左旋转</button>
    <button @click="rotateRight" class="btn">向右旋转</button>
    <button @click="finish('blob')" class="btn">裁剪（Blob）</button>
    <button @click="finish('base64')" class="btn">裁剪（Base64）</button>
    <input type="file" accept="image/*" @change="uploadImg" />
  </div>
  <div class="preview-container">
    <div
      class="show-preview"
      :style="{
        width: previews.w + 'px',
        height: previews.h + 'px',
        overflow: 'hidden',
        margin: '5px',
      }"
    >
      <div :style="previews.div">
        <img :src="previews.url" :style="previews.img" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 20px;
}

.cropper-container {
  width: 70%;
  height: 600px;
}

.preview-container {
  margin-top: 100px;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.button-container {
  display: flex;
  gap: 10px;
  margin-top: 10px;
  flex-wrap: wrap;
}

.btn {
  padding: 5px 10px;
  cursor: pointer;
}
</style>
