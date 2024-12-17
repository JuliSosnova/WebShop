<template>
  <div id="app">
    <nav class="bg-indigo-500 py-4 shadow-xl flex justify-between px-6"> 
      <router-link class="no-underline item text-white" to="/listCategories">Категории</router-link>
      <router-link class="no-underline item text-white" to="/listProducts">Список Товаров</router-link>
      <router-link class="no-underline item text-white" to="/SelectProducts">Корзина Товаров</router-link>
      <div class="flex items-center">
        <div v-if="userRole == 'Администратор'">
            <router-link to="/registerAdmin" class="no-underline px-7 py-2 font-semibold rounded-full text-white">
              Зарегистрировать
            </router-link>  
        </div>
        <div v-if="currentUser">
          <router-link to="/profile" class="text-white mr-4">
            {{ currentUser.username }}
          </router-link>
         
          <a href @click.prevent="logOut" class="no-underline px-6 py-2 font-semibold rounded-full bg-white text-indigo-400">
            Выйти
          </a>
        </div>
        <div v-else>
          <router-link class="no-underline px-7 py-2 font-semibold rounded-full bg-white " to="/login">
            Войти
          </router-link>
        </div>
       
      </div>
     
    </nav>
  </div>
</template>

  
<script>
  import { computed } from 'vue'; 
  import { useStore } from 'vuex';
  import { ref, onMounted } from 'vue';
  export default {
    name: "NavBar",
    setup() {
      const store = useStore();

      const currentUser = computed(() => store.state.auth.user);

      const logOut = () => {
        store.dispatch('auth/logout')
          .then(() => {
            window.location.href = '/login'; 
          })
          .catch(err => {
            console.error("Ошибка выхода:", err); 
          });
      };
      
    const userRole = ref('');

    onMounted(async () => {
    try {
      if (currentUser && currentUser.value.role){
        userRole.value = currentUser.value.role;
      }
    } catch (error) {
      console.error('Ошибка при получении роли пользователя:', error);
    }})

      return {
        currentUser,
        logOut,
        userRole
      };
      
    }
  };
  
  

</script>
  
  <style>
    .item {
      margin-right: 5px;
    }
</style>