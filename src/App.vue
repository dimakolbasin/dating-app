<template>
  <main class="landing" id="main">
    <div class="landing__container">
      <img :draggable="false" class="landing__community" src="/src/assets/group.png" alt="Community members" />

      <section class="landing__guide" aria-labelledby="guide-title">
        <div class="landing__guide-content">
          <h1 class="landing__guide-title" id="guide-title">How to<br>Participate</h1>

          <div class="landing__steps">
            <div class="landing__step">
              <span class="landing__step-number">1.</span>
              <span class="landing__step-description">Subscribe to our News</span>
            </div>

            <div class="landing__step">
              <div class="landing__step-content">
                <span class="landing__step-number">2.</span>
                <Button variant="primary" type="button" @click="openModal">
                  Sign Up
                </Button>
              </div>
            </div>

            <div class="landing__step">
              <span class="landing__step-number">3.</span>
              <span class="landing__step-description">Check your email inbox</span>
            </div>

            <div class="landing__step">
              <span class="landing__step-number">4.</span>
              <span class="landing__step-description">Wait till September 22</span>
            </div>
          </div>
        </div>
      </section>
    </div>

    <Modal v-if="isModalOpen" v-model:open="isModalOpen" @close="closeModal">
      <template #default>
        <Form v-if="!isThankYou" @success="onSuccess" />
        <ThankYou v-else />
      </template>
    </Modal>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import Modal from './components/Modal.vue';
import Form from './components/Form.vue';
import ThankYou from './components/ThankYou.vue';
import Button from './components/Button.vue';

const DATING_AUTH_URL = 'https://www.dating.com/people/#token='
const TIME_COOKIE = 60 * 5

const isModalOpen = ref(false);
const isThankYou = ref(false);

function openModal() {
  isThankYou.value = false;
  isModalOpen.value = true;
}
function closeModal() {
  isModalOpen.value = false;
}

function setCookie(name: string, value: string, maxAgeSeconds: number) {
  document.cookie = `${encodeURIComponent(name)}=${encodeURIComponent(value)}; max-age=${maxAgeSeconds}; path=/; secure; samesite=strict`;
}

function getCookie(name: string): string | null {
  const match = document.cookie.match(new RegExp(`(?:^|; )${encodeURIComponent(name)}=([^;]*)`));
  return match ? decodeURIComponent(match[1]) : null;
}

function redirectToDating(time: number = 0) {
  const t = getCookie('auth_token');
  if (t) {
    setTimeout(() => {
      window.location.href = `${DATING_AUTH_URL}${encodeURIComponent(t)}`;
    }, time)
  }
}

function onSuccess(token: string | null) {
  isThankYou.value = true;
  if (token) {
    try {
      setCookie('auth_token', token, TIME_COOKIE);
    } catch (_) {}
  }

  redirectToDating(2000)
}

onMounted(() => {
  redirectToDating(0)
});
</script>

<style scoped lang="scss">
@import 'styles/adaptive';

$color-text: #3A3A3A;
$text-gradient-primary: linear-gradient(177deg, rgba(229, 23, 38, 1) 0%, rgba(212, 32, 140, 1) 100%);

$font-weight-extrabold: 800;
$font-weight-semibold: 600;

.landing {
  white-space: nowrap;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100svh;
  overflow: hidden;

  &__container {
    display: flex;
  }

  &__community {
    width: auto;
    object-fit: contain;
    aspect-ratio: 568 / 538;
  }

  &__guide {
    width: fit-content;
  }

  &__guide-content {
    width: fit-content;
  }

  &__steps {
    display: flex;
    flex-direction: column;
  }

  &__guide-title {
    font-weight: $font-weight-extrabold;
    background: $text-gradient-primary;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  &__step,
  &__step-content {
    display: flex;
    align-items: center;
  }

  &__step-number {
    font-weight: $font-weight-extrabold;
    background: $text-gradient-primary;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  &__step-description {
    font-weight: $font-weight-semibold;
    color: $color-text;
  }

  @media #{$landscape-media} {
    padding-right: landscape-size(200);

    &__container {
      gap: landscape-size(284);
    }

    &__community {
      height: landscape-size(689);
    }

    &__steps {
      gap: landscape-size(16);
    }

    &__guide-title {
      @include font-responsive(landscape-size(77), 110%);
      margin-top: landscape-size(40);
      letter-spacing: landscape-size(0.5);
    }

    &__step,
    &__step-content {
      gap: landscape-size(18);
    }

    &__step-number {
      @include font-responsive(landscape-size(55), 100%);
      letter-spacing: landscape-size(0.5);
    }

    &__step-description {
      @include font-responsive(landscape-size(38), 198%);
      letter-spacing: landscape-size(0.5);
    }
  }

  @media #{$portrait-media} {
    padding: portrait-size(15);

    &__container {
      flex-direction: column;
      align-items: center;
      gap: portrait-size(20);
    }

    &__community {
      height: portrait-size(274);
    }

    &__steps {
      gap: portrait-size(16);
    }

    &__guide-title {
      @include font-responsive(portrait-size(35), 110%);
      margin-top: portrait-size(0);
    }

    &__step,
    &__step-content {
      gap: portrait-size(18);
    }

    &__step-number {
      @include font-responsive(portrait-size(33), 100%);
    }

    &__step-description {
      @include font-responsive(portrait-size(18), 198%);
    }
  }
}
</style>