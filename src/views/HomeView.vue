<template>
  <main class="main">
    <input type="text" class="search" placeholder="search by name" v-model="search" />
    <section class="users" v-if="actualUsers.length">
      <article class="user" v-for="user in actualUsers" :key="`user-${user.id}`">
        <div class="image">
          <img :src="user.avatar" alt="user" />
        </div>
        <!-- <div class="image" v-else>
          <img src="../assets/images/some2.png" alt="user" />
        </div> -->
        <p @click="setActiveDetail(user.id)" class="link">{{ user.first_name }}</p>
        <p>{{ user.last_name }}</p>
        <p>{{ user.email }}</p>
        <button @click="deleteUser(user.id)" :disabled="isDeleteProcecing">delete</button>
      </article>
    </section>
    <section v-else-if="loading">
      <Loader />
    </section>
    <section v-else-if="error">
      <h2>Something went wrong</h2>
    </section>
    <section v-else>
      <h2>there is no users for now</h2>
    </section>
    <section class="form">
      <button @click="toggleAddModal" class="create">create user</button>
    </section>
    <AddFrom v-if="addModal" @close="toggleAddModal" @alert="userAddedAlert" />
    <UserDetails :id="detailId" @close="toggleDetailModal" v-if="detailModal" />
    <p class="alert" v-show="userAdded">User was added</p>
    <p class="alert-delete" v-show="userDeleted">User was deleted</p>
  </main>
</template>

<script setup>
import axios from 'axios'
import Loader from '../components/loaders/Loader.vue'
import AddFrom from '../components/modals/AddFrom.vue'
import UserDetails from '../components/modals/UserDetails.vue'
import { onMounted, ref, computed } from 'vue'

const search = ref('')

const users = ref([])
const loading = ref(false)
const error = ref(false)

const addModal = ref(false)
const userAdded = ref(false)

const isDeleteProcecing = ref(false)
const isDeleteError = ref(false)
const userDeleted = ref(false)

const detailId = ref(null)
const detailModal = ref(false)

const actualUsers = computed(() => {
  if (!search.value) {
    return users.value
  }

  return users.value.filter((el) => el.first_name.includes(search.value))
})

const setActiveDetail = (id) => {
  detailId.value = id
  toggleDetailModal()
}

const toggleAddModal = () => {
  addModal.value = !addModal.value
}

const toggleDetailModal = () => {
  detailModal.value = !detailModal.value
}

const deleteUser = async (id) => {
  try {
    isDeleteError.value = false
    isDeleteProcecing.value = true
    await axios.delete(`https://reqres.in/api/users/${id}`)
    await getUsers()
    userDeletedAlert()
  } catch (e) {
    isDeleteError.value = true
  } finally {
    isDeleteProcecing.value = false
  }
}

const userAddedAlert = () => {
  userAdded.value = true
  setTimeout(() => {
    userAdded.value = false
  }, 1000)
}

const userDeletedAlert = () => {
  userDeleted.value = true
  setTimeout(() => {
    userDeleted.value = false
  }, 1000)
}

const getUsers = async () => {
  try {
    loading.value = true
    error.value = false
    const {
      data: { data }
    } = await axios.get('https://reqres.in/api/users?per_page=12')
    users.value = data
  } catch (e) {
    error.value = true
    users.value = []
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  getUsers()
})
</script>
<style scoped lang="scss">
.search {
  margin-top: 50px;
}
.alert,
.alert-delete {
  background-color: green;
  color: #fff;
  padding: 15px;
  position: fixed;
  right: 15px;
  bottom: 15px;
}
.alert-delete {
  background-color: red;
}
.main {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.users {
  margin-top: 50px;
  display: flex;
  gap: 15px;
  flex-wrap: wrap;
  .user {
    text-align: center;
    border: 1px solid black;
    border-radius: 5px;
    .link {
      cursor: pointer;
      text-decoration: underline;
      color: blue;
    }
  }
}
.form {
  margin-top: 50px;
}
</style>
