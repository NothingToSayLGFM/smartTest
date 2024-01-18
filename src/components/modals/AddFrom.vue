<template>
  <div class="modal">
    <button class="close" @click="close">close</button>
    <div class="modal-wrap">
      <form @submit.prevent="createUser">
        <div>
          <label for="name">name</label>
          <input v-model="name" id="name" type="text" :class="{ 'error-b': emptyName }" />
        </div>
        <div>
          <label for="email">email</label>
          <input v-model="email" id="email" type="email" :class="{ 'error-b': emptyEmail }" />
        </div>
        <button type="submit" :disabled="loading || emptyEmail || emptyName">submit</button>
        <p class="error" v-if="error">something went wrong</p>
      </form>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { computed, ref } from 'vue'

const emit = defineEmits(['close', 'alert'])
const loading = ref(false)
const error = ref(false)
const name = ref('')
const email = ref('')

const close = () => {
  emit('close')
}

const emptyName = computed(() => {
  return !name.value
})
const emptyEmail = computed(() => {
  return (
    !email.value ||
    !email.value.match(
      /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
    )
  )
})

const createUser = async () => {
  if (!name.value || !email.value) {
    return
  }
  error.value = false
  loading.value = true
  try {
    await axios.post('https://reqres.in/api/users', {
      name: name.value,
      email: email.value
    })
    emit('alert')
    close()
  } catch (e) {
    error.value = true
  } finally {
    loading.value = false
  }
}
</script>

<style scoped lang="scss">
label {
  display: block;
}
.error {
  color: red;
}

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
}
.modal-wrap {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  form {
    font-size: 26px;
    background-color: #fff;
    padding: 15px;
    width: 500px;
    input {
      width: 100%;
      font-size: 26px;
    }
    button {
      font-size: 26px;
      margin-top: 20px;
    }
  }
}
.error-b {
  border-color: red;
}
</style>
