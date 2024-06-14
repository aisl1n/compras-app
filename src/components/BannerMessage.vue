<template>
    <q-banner inline-actions :class="bannerClass" :style="{ display: displayStyle, 'border-radius': '8px' }">
      <p class="banner-in">{{ message }}</p>
    </q-banner>
  </template>
  
  <script>
  import { defineComponent, ref } from 'vue'
  
  export default defineComponent({
    name: 'BannerMessage',
    props: {
      message: {
        type: String,
        required: true
      },
      type: {
        type: String,
        default: 'success' // Pode ser 'success' ou 'error'
      }
    },
    setup() {
      const displayStyle = ref('block');
      setTimeout(() => {
        displayStyle.value = 'none';
      }, 5000);
  
      return {
        displayStyle
      }
    },
    computed: {
      bannerClass() {
        return this.type === 'success' ? 'bg-green-7 text-white text-bold' : 'bg-red-7 text-white text-bold';
      }
    }
  })
  </script>
  
  <style>
  /* Estilos adicionais do banner, se necessário */
  </style>
  