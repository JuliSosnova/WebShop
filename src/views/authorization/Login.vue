<template>
    <div class="col-md-5 mx-auto mt-4">
        <h4 class="text-center">Вход в систему</h4>
        <form @submit.prevent="handleLogin">
            <div class="form-group mb-3">
                <input type="text" class="form-control" placeholder="Логин" v-model="user.username" required />
            </div>
            <div class="form-group mb-3">
                <input type="password" class="form-control" placeholder="Пароль" v-model="user.password" required />
            </div>
            <div class="form-group text-center">
                <button class="btn btn-primary  btn-block" :disabled="loading">
                    <span v-if="loading" class="spinner-border spinner-border-sm"></span>
                    <span>Войти</span>
                </button>
            </div>
            <router-link to="/register">Зарегистрироваться</router-link>
            <div class="form-group">
                <div v-if="message" class="alert alert-danger" role="alert">{{ message }}</div>
            </div>
        </form>
    </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import { useStore } from 'vuex';

export default {
    name: 'LoginUser',
    setup() {
        const store = useStore();
        const user = ref({ username: '', password: '' });
        const loading = ref(false);
        const message = ref('');

        const loggedIn = computed(() => store.state.auth.status.loggedIn);

        onMounted(() => {
            if (loggedIn.value) { // проверяем, авторизован ли пользователь
                window.location.href = '/'; // перенаправляем на главную страницу
            }
        });

        const handleLogin = async () => {
            loading.value = true;
            message.value = ''; 

            try {
                await store.dispatch('auth/login', user.value); // вызываем метод login(...) из auth.service.js 
                window.location.href = '/'; // перенаправляем на главную страницу после успешного входа
            } catch (error) {
                loading.value = false; // устанавливаем состояние загрузки в false при ошибке
                message.value = error.response.data.message; // устанавливаем сообщение об ошибке из ответа сервера
            }
        };

        return {
            user, 
            loading,
            message,
            handleLogin
        };
    }
};
</script>