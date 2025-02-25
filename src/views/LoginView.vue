<script setup>
  import { ref, computed, onMounted  } from 'vue'
  import { getAPIData, API_PORT } from '@/utils/apis.js'
  import { useCounterStore } from '@/stores/counter'
  const email = ref('')
  const password = ref('')
  const isDisabled = computed(() => email.value === '' || password.value === '')
  const props = defineProps(['fecha'])
  const emit = defineEmits(['login-form-submit'])
  const store = useCounterStore()

  async function onFormSubmit(e) {
    console.log('submit', e)
    const loginData = {
      email: email.value,
      password: password.value
    }
    let eventDetail
    if (loginData.email !== '' && loginData.password !== '') {
      const payload = JSON.stringify(loginData)
      let apiData = await getAPIData(`${location.protocol}//${location.hostname}${API_PORT}/api/login`, 'POST', payload)
      if (!apiData) {
        apiData = {
          detail: {
            text: 'No he encontrado el usuario o la contraseña'
          }
        }
      }
      eventDetail = apiData
    } else {
      eventDetail = {
        text: 'No he encontrado el usuario o la contraseña'
      }
    }
    emit('login-form-submit', eventDetail)
  }

  function onButtonClick() {
    store.increment()
  }

  onMounted(() => {
    console.log(`the component is now mounted.`)
  })
</script>

<template>
  <h1>Login ({{ props.fecha }}) {{ store.count }}</h1>
  <form id="loginForm" @submit.prevent="onFormSubmit">
    <label>Usuario: <input type="text" id="email" placeholder="email"
      v-model="email"
      :class="{ 'error': email === '' }" /></label>
    <label>Contraseña: <input type="password" id="password" placeholder="contraseña"
      v-model="password"
      :class="{ 'error': password === '' }" /></label>
    <button type="submit" id="loginButton" title="Login" :disabled="isDisabled" @click="onButtonClick">Login</button>
  </form>
</template>

<style scoped>
form {
  margin-top: 1em;
  display: flex;
  gap: 10px 5px;
  flex-flow: row wrap;
  padding: calc(var(--border-radius)*2);
  border-radius: var(--border-radius);
  background-color: var(--card-bg-color);

  &>label {
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    row-gap: 5px;

    input {
      border-radius: var(--border-radius);
      padding: 0 5px;
      line-height: 40px;
      font-size: 24px;
      flex-grow: 1;

      &.error {
        border-color: red;
      }

      &[type="number"] {
        text-align: right;
      }
    }
  }
}

@media screen and (max-width: 768px) {
  form {
    flex-wrap: wrap;

    label {
      width: 48%;
      max-width: 48%;
    }

    &#articleForm {
      label {
        &:has(>input#articleName) {
          min-width: 100px;
          max-width: 100%;
          width: 100%;
        }
      }
    }

    &#loginForm {
      label {
        min-width: 100px;
        max-width: 100%;
        width: 100%;
      }
    }

    &#menuForm {
      label {
        &:has(>input#menuName),
        &:has(>input#menuArticleName) {
          min-width: 100px;
          max-width: 100%;
          width: 100%;
        }
      }
    }

    button {
      line-height: 2em;
      flex-grow: 1;

      &.icon-button {
        height: 66px;
        width: 66px;
        max-width: 66px;
        margin-left: 5px;

        &+.icon-button {
          margin-left: 15px;
        }
      }
    }
  }
}

@media screen and (min-width: 769px) {
  form {
    gap: 20px 5px;

    &#loginForm {
      label {
        min-width: 80px;
        max-width: calc(100% / 2 - 50px);
        width: 100%;
      }
    }

    &#menuForm {
      label {
        min-width: 50px;
        max-width: calc(100% / 4);

        &:has(>input#menuName) {
          max-width: 100%;
          width: 100%;
        }

        &:has(>input#menuArticleName) {
          min-width: 100px;
          max-width: calc(calc(100% - 75px)/2);
          width: 100%;
        }
      }

    }

    &#articleForm {
      label {
        min-width: 50px;
        max-width: calc(100% / 8 + 14px);

        &:has(>input#articleName) {
          min-width: 100px;
          max-width: calc(calc(100% - 200px)/1.5);
          width: 100%;
        }
      }
    }
  }
}
</style>
