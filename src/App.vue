<script setup>
import { ref, computed } from 'vue';
import { vMaska } from 'maska';
import * as yup from 'yup';
import { Form, Field, ErrorMessage } from 'vee-validate';

const schema = yup.object({
  name: yup.string().required('Это поле обязательно').min(3, 'Слишком короткое имя'),
  email: yup
    .string()
    .required('Это поле обязательно')
    .email('Некорректный адрес электронной почты'),
  phone: yup.string().required('Это поле обязательно').min('18', 'Некорректный номер телефона'),
  file: yup.mixed().required('Файл обязателен'),
  consent: yup.bool().required('Согласие обязательно'),
});

const form = ref({
  name: '',
  phone: '',
  email: '',
  file: null,
  consent: null,
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

  if (!file) {
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
  setTimeout(() => (message.value = ''), 3000);
}

// проверка ошибок во всех полях

function hasErrors(errors) {
  return Object.keys(errors).length > 0;
}
</script>

<template>
  <main class="main">
    <button class="button" @click="showForm = true">Оставить отзыв</button>
    <p class="message" v-if="message">{{ message }}</p>

    <div v-if="showForm" class="modal" @click.self="showForm = false">
      <div class="content">
        <span class="close" @click="showForm = false">&times;</span>
        <Form class="form" @submit="submitForm" :validation-schema="schema" v-slot="{ errors }">
          <h2 class="title">Оставьте отзыв</h2>

          <Field
            type="text"
            id="name"
            name="name"
            v-model="form.name"
            placeholder="Ваше имя"
            required />

          <span class="error-span"><ErrorMessage name="name" class="error" /></span>

          <Field
            type="tel"
            id="phone"
            name="phone"
            v-model="form.phone"
            placeholder="Ваш номер телефона"
            required
            v-maska
            data-maska="+7 (###) ###-##-##" />

          <span class="error-span"><ErrorMessage name="phone" class="error" /></span>

          <Field
            type="email"
            id="email"
            name="email"
            v-model="form.email"
            placeholder="Ваш email"
            required />

          <span class="error-span"><ErrorMessage name="email" class="error" /></span>

          <Field
            type="file"
            id="file"
            name="file"
            @change="onFileInput"
            accept="image/*,application/pdf"
            placeholder="Прикрепить файл"
            required />

          <span class="error-span"><ErrorMessage name="file" class="error" /></span>

          <div>
            <Field
              class="consent"
              type="checkbox"
              id="consent"
              name="consent"
              v-model="form.consent"
              :value="true"
              required />
            <label for="consent">Согласие на обработку персональных данных</label>
          </div>
          <span class="error-span"><ErrorMessage name="consent" class="error" /></span>

          <button
            class="button"
            type="submit"
            :disabled="!isFormValid || hasErrors(errors)"
            :class="{ disabled: !isFormValid || hasErrors(errors) }">
            Отправить
          </button>
        </Form>
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
}

.title {
  margin-bottom: 15px;
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

.error-span {
  min-height: 18px;
}
.error {
  color: red;
  font-size: 12px;
  line-height: 16px;
}
</style>
