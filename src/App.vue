<template>
  <div class="image-uploader">
    <input type="file" @change="onFileChange" accept="image/*" />
    <div v-if="imageUrl" class="preview">
      <img :src="imageUrl" alt="Uploaded Image" />
      <button @click="uploadImage">Upload</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const imageUrl = ref(null);
const imageFile = ref(null);

const onFileChange = (event) => {
  const file = event.target.files[0];
  if (file) {
    imageFile.value = file;
    const reader = new FileReader();
    reader.onload = () => {
      imageUrl.value = reader.result;
    };
    reader.readAsDataURL(file);
  }
};

const uploadImage = async () => {
  if (!imageFile.value) return;
  const formData = new FormData();
  formData.append("image", imageFile.value);

  try {
    const response = await axios.post(
      "http://localhost:8000/check_copyright",
      formData,
      {
        headers: {
          "Content-Type": "multipart/form-data",
        },
      }
    );
    console.log("Upload successful:", response.data);
  } catch (error) {
    console.error("Upload failed:", error);
  }
};
</script>

<style scoped>
.image-uploader {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}
.preview img {
  max-width: 300px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
button {
  margin-top: 10px;
  padding: 8px 16px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  background-color: #0056b3;
}
</style>
