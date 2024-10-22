<template>
    <div class="modal" v-if="show">
      <div class="modal-content">
        <h2>添加餐厅</h2>
        <form @submit.prevent="submitForm">
          <div class="form-group">
            <label for="name">昵称: </label>
            <input v-model="name" type="text" id="name" required />
          </div>
  
          <div class="form-group">
            <label for="slogan">口号: </label>
            <input v-model="slogan" type="text" id="slogan" required />
          </div>
  
          <div class="form-group">
            <label for="avatar">选择图片: </label>
            <input @change="handleFileChange" type="file" id="avatar" accept="image/*" required />
          </div>
  
          <div class="modal-actions">
            <button type="submit">添加</button>
            <button @click="closeModal">取消</button>
          </div>
        </form>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref } from 'vue';
  
  export default defineComponent({
    name: 'AddDishModal',
    props: {
      show: {
        type: Boolean,
        required: true
      }
    },
    emits: ['close', 'add-dish'],
    setup(props, { emit }) {
      const name = ref<string>('');
      const slogan = ref<string>('');
      const picUrl = ref<string>('');
  
      const handleFileChange = (event: Event) => {
        const target = event.target as HTMLInputElement;
        const file = target.files?.[0];
        if (file) {
          const reader = new FileReader();
          reader.onload = (e: ProgressEvent<FileReader>) => {
            picUrl.value = e.target?.result as string;
          };
          reader.readAsDataURL(file);
        }
      };
  
      const submitForm = () => {
        if (name.value && slogan.value && picUrl.value) {
          emit('add-dish', { name: name.value, slogan: slogan.value, picUrl: picUrl.value });
          closeModal();
        }
      };
  
      const closeModal = () => {
        emit('close');
      };
  
      return {
        name,
        slogan,
        picUrl,
        handleFileChange,
        submitForm,
        closeModal
      };
    }
  });
  </script>
  
  <style scoped>
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 2; /* 保证其他元素能在弹幕后方显示 */
  }
  
  .modal-content {
    background-color: white;
    padding: 20px;
    border-radius: 10px;
    width: 300px;
  }
  
  .form-group {
    margin-bottom: 10px;
  }
  
  .modal-actions {
    display: flex;
    justify-content: space-between;
  }
  </style>
  