<template>
  <div class="modal">
    <button class="close" @click="close">close</button>
    <div class="modal-wrap">
      <div class="detail" v-if="user">
        <img :src="user.avatar" alt="user" />
        <p>{{ user.email }}</p>
        <p>{{ user.first_name }}</p>
        <p>{{ user.last_name }}</p>
        <p>{{ user.id }}</p>
      </div>
      <div v-else-if="loading">
        <Loader />
      </div>
      <div v-else-if="error">
        <p>something went wrong</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'
import Loader from '../loaders/Loader.vue'

const props = defineProps({
  id: {
    required: true,
    type: Number
  }
})

const emit = defineEmits(['close'])

const loading = ref(false)
const error = ref(false)
const user = ref(null)

const getUser = async () => {
  try {
    error.value = false
    loading.value = true
    const {
      data: { data }
    } = await axios.get(`https://reqres.in/api/users/${props.id}`)
    user.value = data
  } catch (e) {
    error.value = true
  } finally {
    loading.value = false
  }
}

const close = () => {
  emit('close')
}

onMounted(() => {
  getUser()
})
</script>

<style lang="scss" scoped>
.modal {
  background-color: rgba(0, 0, 0, 0.8);
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  .close {
    font-size: 20px;
    position: absolute;
    right: 15px;
    top: 15px;
  }
  .modal-wrap {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    .detail {
      background-color: #fff;
      padding: 15px;
      text-align: center;
    }
  }
}
</style>
