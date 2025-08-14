<template>
  <div class="form">
    <p class="form__instruction">To register, enter the mail to which our news is sent and set your password</p>

    <form class="form__form" novalidate @submit.prevent="onSubmit" aria-describedby="form-error" data-testid="form-signup">
    <div v-if="formError" class="form__error" id="form-error" role="alert">{{ formError }}</div>

      <div class="form__field">
        <div class="form__control">
          <input
            :id="ids.email"
            class="form__input"
            type="email"
            name="email"
            inputmode="email"
            autocomplete="email"
            placeholder="Example@email.com"
            v-model.trim="email"
            :aria-invalid="!!errors.email"
            :aria-describedby="errors.email ? ids.emailError : undefined"
            required
          />
          <div v-if="errors.email" class="form__icon" aria-hidden="true">
            <svg width="25" height="25" viewBox="0 0 25 25" fill="none" xmlns="http://www.w3.org/2000/svg">
              <g clip-path="url(#clip0_1_695)">
                <path d="M12.4756 2.5C6.95559 2.5 2.47559 6.98 2.47559 12.5C2.47559 18.02 6.95559 22.5 12.4756 22.5C17.9956 22.5 22.4756 18.02 22.4756 12.5C22.4756 6.98 17.9956 2.5 12.4756 2.5ZM13.4756 17.5H11.4756V15.5H13.4756V17.5ZM13.4756 13.5H11.4756V7.5H13.4756V13.5Z" fill="#FF0000"/>
              </g>
              <defs>
                <clipPath id="clip0_1_695">
                  <rect width="24" height="24" fill="white" transform="translate(0.475586 0.5)"/>
                </clipPath>
              </defs>
            </svg>
          </div>
        </div>
        <p v-if="errors.email" class="form__hint form__hint--error" :id="ids.emailError">{{ errors.email }}</p>
      </div>

      <div class="form__field">
        <div class="form__control">
          <input
            :id="ids.password"
            class="form__input"
            type="password"
            name="password"
            autocomplete="new-password"
            placeholder="Password"
            v-model="password"
            minlength="8"
            maxlength="8"
            :aria-invalid="!!errors.password"
            :aria-describedby="errors.password ? ids.passwordError : undefined"
            required
          />
          <div v-if="errors.password" class="form__icon" aria-hidden="true">
            <svg width="25" height="25" viewBox="0 0 25 25" fill="none" xmlns="http://www.w3.org/2000/svg">
              <g clip-path="url(#clip0_1_695)">
                <path d="M12.4756 2.5C6.95559 2.5 2.47559 6.98 2.47559 12.5C2.47559 18.02 6.95559 22.5 12.4756 22.5C17.9956 22.5 22.4756 18.02 22.4756 12.5C22.4756 6.98 17.9956 2.5 12.4756 2.5ZM13.4756 17.5H11.4756V15.5H13.4756V17.5ZM13.4756 13.5H11.4756V7.5H13.4756V13.5Z" fill="#FF0000"/>
              </g>
              <defs>
                <clipPath id="clip0_1_695">
                  <rect width="24" height="24" fill="white" transform="translate(0.475586 0.5)"/>
                </clipPath>
              </defs>
            </svg>
          </div>
        </div>
        <p v-if="errors.password" class="form__hint form__hint--error" :id="ids.passwordError">{{ errors.password }}</p>
      </div>

      <div class="form__actions">
        <Button
          variant="primary"
          type="submit"
          :disabled="loading"
        >
          <template #default>
            <span v-if="!loading" class="form__btn-text">submit</span>
            <span v-else class="">Submitting…</span>
          </template>
        </Button>
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import {reactive, ref, useId, watch} from 'vue';
import Button from "~/components/Button.vue";

const API_IDENTITY_URL = 'https://api.dating.com/identity';

const emit = defineEmits<{ (e: 'success', token: string | null): void }>();

const email = ref('');
const password = ref('');
const loading = ref(false);
const formError = ref<string>('');

const errors = reactive<{ email: string; password: string }>({ email: '', password: '' });

const ids = {
  email: `email-${useId()}`,
  password: `password-${useId()}`,
  emailError: `email-err-${useId()}`,
  passwordError: `password-err-${useId()}`,
};

function isValidEmail(value: string) {
  const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return re.test(value.trim());
}

function isStrongPassword(value: string) {
  return value.length === 8;
}

function validate() {
  errors.email = '';
  errors.password = '';
  formError.value = '';

  if (!isValidEmail(email.value)) {
    errors.email = 'Enter a valid email address.';
  }
  if (!isStrongPassword(password.value)) {
    errors.password = 'Password must contain exactly 8 characters.';
  }
  return !errors.email && !errors.password;
}

watch(email, (v) => {
  errors.email = isValidEmail(v) ? '' : (v ? 'Enter a valid email address.' : '');
});

watch(password, (v) => {
  errors.password = isStrongPassword(v) ? '' : (v ? 'Password must contain exactly 8 characters.' : '');
});

async function onSubmit() {
  if (!validate()) return;
  loading.value = true;
  try {
    const basic = btoa(`${email.value}:${password.value}`);
    const getResp = await fetch(API_IDENTITY_URL, {
      method: 'GET',
      headers: {
        Authorization: `Basic ${basic}`,
        Accept: 'application/json',
      },
      mode: 'cors',
      credentials: 'omit',
    });

    if (getResp.ok) {
      const token = getResp.headers.get('X-Token');
      emit('success', token);
      return;
    }

    const putResp = await fetch(API_IDENTITY_URL, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
        Accept: 'application/json',
      },
      body: JSON.stringify({ email: email.value, password: password.value }),
      mode: 'cors',
      credentials: 'omit',
    });

    if (putResp.ok) {
      const token = putResp.headers.get('X-Token');
      emit('success', token);
    } else {
      try {
        const data = await putResp.json();
        formError.value = data?.message || 'Registration failed. Please try again.';
      } catch {
        formError.value = 'Registration failed. Please try again.';
      }
    }
  } catch (e) {
    formError.value = 'Network error. Please try again later.';
  } finally {
    loading.value = false;
  }
}
</script>

<style scoped lang="scss">
@import '~/styles/adaptive';

$color-text-dark: #3A3A3A;
$color-placeholder: #B3B3B3;
$color-error: #FF0000;
$font-weight-bold: 700;

.form {
  width: 100%;

  &__form {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  &__instruction {
    color: $color-text-dark;
    white-space: normal;
    text-align: center;
    font-weight: $font-weight-bold;
  }

  &__field {
    width: 100%;
  }

  &__control {
    position: relative;
  }

  &__input {
    width: 100%;
    background: transparent;
    border: none;
    border-bottom-style: solid;
    border-bottom-color: var(--color-border);
    border-radius: 0;
    outline: none;
    box-shadow: none;
    text-align: left;

    &:focus {
      border-bottom-color: var(--color-text);
    }

    &:placeholder-shown {
      text-align: center;
    }

    &::placeholder {
      color: $color-placeholder;
      text-align: center;
    }
  }

  &__icon {
    position: absolute;
    right: 0;
    top: 50%;
    transform: translateY(-50%);
    display: inline-flex;
    align-items: center;
    justify-content: center;
    pointer-events: none;
  }

  &__hint {
    text-align: center;

    &--error {
      color: $color-error;
    }
  }

  @media #{$landscape-media} {
    max-width: landscape-size(620);
    @include font-responsive(landscape-size(18), landscape-size(24));

    &__input::placeholder {
      @include font-responsive(landscape-size(18), landscape-size(24));
    }

    &__btn-text {
      letter-spacing: landscape-size(4);
    }

    &__form {
      gap: landscape-size(55);
    }

    &__instruction {
      @include font-responsive(landscape-size(24), landscape-size(28));
    }

    &__input {
      padding-top: landscape-size(6);
      padding-bottom: landscape-size(6);
      border-bottom-width: landscape-size(1);
    }

    &__hint {
      @include font-responsive(landscape-size(12), landscape-size(18));
    }

    &__icon {
      width: landscape-size(25);
      height: landscape-size(25);
    }
  }

  @media #{$portrait-media} {
    padding: 0 portrait-size(15);
    padding-bottom: portrait-size(45);
    @include font-responsive(portrait-size(14), portrait-size(22));

    &__input::placeholder {
      @include font-responsive(portrait-size(14), portrait-size(22));
    }

    &__btn-text {
      letter-spacing: portrait-size(1);
    }

    &__instruction {
      @include font-responsive(portrait-size(17), portrait-size(24));
    }

    &__input {
      padding-top: portrait-size(6);
      padding-bottom: portrait-size(6);
      border-bottom-width: portrait-size(1);
    }

    &__form {
      gap: portrait-size(35);
    }

    &__hint {
      @include font-responsive(portrait-size(12), portrait-size(18));
    }

    &__icon {
      width: portrait-size(25);
      height: portrait-size(25);
    }
  }
}
</style>
