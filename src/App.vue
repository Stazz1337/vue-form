<script setup>
import { ref, computed } from 'vue';

const form = ref({
  name: '',
  phone: '',
  email: '',
  file: null,
  consent: false,
});

const showForm = ref(false);

const message = ref('');

const isFormValid = computed(() => {
  return (
    form.value.name && form.value.phone && form.value.email && form.value.file && form.value.consent
  );
});

// обработчик добавления файла

function onFileInput(e) {
  const file = e.target.files[0];

  if (file.size > 2097152) {
    alert('Допустимый размер файла не более 2 МБ');
    form.value.file = null;
    e.target.value = '';
    return;
  }

  if (!file.type.includes('image') && !file.type.includes('application/pdf')) {
    alert('Пожалуйста, выберите изображение или pdf-файл');
    form.value.file = null;
    e.target.value = '';
    return;
  }

  form.value.file = file;
}

// обработчик отправки формы

function submitForm() {
  console.log(form.value);
  showForm.value = false;
  form.value = {
    name: '',
    phone: '',
    email: '',
    file: null,
    consent: false,
  };
  message.value = 'Отзыв записан в консоль';
  setTimeout(() => (message.value = ''), 2000);
}
</script>

<template>
  <main class="main">
    <button class="button" @click="showForm = true">Оставить отзыв</button>
    <p class="message" v-if="message">{{ message }}</p>

    <div v-if="showForm" class="modal" @click.self="showForm = false">
      <div class="content">
        <span class="close" @click="showForm = false">&times;</span>
        <form class="form" @submit.prevent="submitForm">
          <h2>Оставьте отзыв</h2>

          <input
            type="text"
            id="name"
            name="name"
            v-model="form.name"
            placeholder="Ваше имя"
            required />

          <input
            type="tel"
            id="phone"
            name="phone"
            v-model="form.phone"
            placeholder="Ваш номер телефона"
            required />

          <input
            type="email"
            id="email"
            name="email"
            v-model="form.email"
            placeholder="Ваш email"
            required />

          <input
            type="file"
            id="file"
            name="file"
            @change="onFileInput"
            accept="image/*,application/pdf"
            placeholder="Прикрепить файл"
            required />

          <div>
            <input
              class="consent"
              type="checkbox"
              id="consent"
              name="consent"
              v-model="form.consent"
              required />
            <label for="consent">Согласие на обработку персональных данных</label>
          </div>

          <button
            class="button"
            type="submit"
            :disabled="!isFormValid"
            :class="{ disabled: !isFormValid }">
            Отправить
          </button>
        </form>
      </div>
    </div>
  </main>
</template>

<style scoped>
.main {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.button {
  margin: 10% auto 0;
  padding: 10px;
  border-radius: 5px;
  background-color: #20d3ca;
  border: none;
  cursor: pointer;
  font-size: large;
  width: 200px;
}

.button.disabled {
  pointer-events: none;
}

.button:hover {
  opacity: 0.8;
}

.modal {
  background-color: rgba(0, 0, 0, 0.5);
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
}

.content {
  display: flex;
  justify-content: center;
  background-color: #fff;
  margin: 10% auto 0;
  padding: 30px;
  width: 300px;
  position: relative;
  border-radius: 5px;
}

.close {
  color: gray;
  font-size: 40px;
  position: absolute;
  top: -3px;
  right: 5px;
}
.close:hover {
  color: black;
  cursor: pointer;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.consent {
  margin-right: 5px;
}

.message {
  display: block;
  color: green;
  margin: 10px auto 0;
  text-align: center;
}
</style>
