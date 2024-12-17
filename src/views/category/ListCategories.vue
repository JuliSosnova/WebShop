<template>
  <div>
    <div class="flex justify-between items-center px-4">
      <h4 class="text-center py-3">Список категорий</h4>
      <div class="flex items-center"> <!-- Новый контейнер для кнопок -->
        <div v-if="displayContent">
          <router-link 
            class="py-2 no-underline bg-indigo-500 text-white font-semibold rounded hover:bg-indigo-600 transition px-6 mr-2" 
            to="/addCategory">+
          </router-link>
        </div>
        
        <div v-else>
          Добавление доступно только авторизованным пользователям
        </div>
        
        <router-link 
          class="py-2 no-underline bg-indigo-500 text-white font-semibold rounded hover:bg-indigo-600 transition px-7 " 
          to="/searchCategories">🔍
        </router-link>
      </div>
    </div>
  
    <ul >
      <!-- Вывод списка категорий -->
      <li    v-for="(category, index) in categories" :key="index"   >
        <router-link  :to="{
                        name: 'category-details',
                        params: { id: category.id }
                    }"   class="py-2 text-indigo-400 font-semibold no-underline">
          {{category.name}}
        </router-link>
      </li>
    </ul>
  </div>
</template>
<script>
import { defineComponent, ref, onMounted } from 'vue';
import http from "../../http-common"; // подключение объекта, который позволяет отправлять запросы серверу
import UserService from '../../services/user.service';

export default defineComponent({
  name: "ListCategories", // имя шаблона
  setup() {
    const categories = ref([]);
    const displayContent = ref(false); // cостояние для отображения контента

    const getCategories = () => {
      http
          .get("/categories") // обращаемся к серверу для получения списка категорий
          .then(response => { // запрос выполнен успешно
            console.log(response.data)
            categories.value = response.data;
          })
          .catch(e => { // запрос выполнен с ошибкой
            console.log(e);
          });
    };

    onMounted(() => { // вызывается после монтирования компонента
      UserService.getUserBoard()
        .then(() => {
          displayContent.value = true; // если пользователь авторизован, показываем контент
        })
        .catch(e => {
          console.error((e.response && e.response.data) || e.message || e.toString());
        });

        getCategories();
    });

    return {
      categories,
      displayContent,
      getCategories
    };
  }
});
</script>

<style>
.item {
  margin-left: 5px;
}
</style>