<template>
  <dialog
    v-show="open"
    class="modal"
    ref="dialog"
    @cancel.prevent="onCancel"
    @click="onDialogClick"
  >
    <section class="modal__body" @click.stop>
      <button class="modal__close" type="button" aria-label="Close" @click="emitClose" ref="closeBtn">
        <svg class="modal__close-icon" width="30" height="30" viewBox="0 0 30 30" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path fill-rule="evenodd" clip-rule="evenodd" d="M4.41262 0.708832L0.846834 4.27461L11.5443 14.9721L0.84668 25.6697L4.41246 29.2355L15.1101 18.5379L25.8077 29.2354L29.3734 25.6697L18.6759 14.9721L29.3733 4.27469L25.8075 0.708908L15.1101 11.4063L4.41262 0.708832Z" fill="url(#paint0_linear_1_532)"/>
          <defs>
            <linearGradient id="paint0_linear_1_532" x1="13.9828" y1="-2.47944" x2="30.1903" y2="20.7347" gradientUnits="userSpaceOnUse">
              <stop stop-color="#E61726"/>
              <stop offset="1" stop-color="#D4208C"/>
            </linearGradient>
          </defs>
        </svg>

      </button>
      <slot />
    </section>
  </dialog>
</template>

<script setup lang="ts">
import { onMounted, onBeforeUnmount, useTemplateRef } from 'vue';

const open = defineModel('open', {required: false, type: Boolean, default: false});
const emit = defineEmits<{ (e: 'close'): void }>();

const dialogRef = useTemplateRef<HTMLDialogElement>('dialog');

function emitClose() {
  emit('close');
}

function onCancel() {
  open.value = false;
}

function onDialogClick(e: MouseEvent) {
  if (e.target === dialogRef.value) {
    onCancel()
  }
}

function openDialog() {
  if (!dialogRef.value) return;
  if (typeof dialogRef.value.showModal === 'function') {
    if (!dialogRef.value.open) dialogRef.value.showModal();
  } else {
    dialogRef.value.setAttribute('open', '');
  }
}

function closeDialog() {
  if (!dialogRef.value) return;
  if (dialogRef.value.open && typeof dialogRef.value.close === 'function') {
    dialogRef.value.close();
  } else {
    dialogRef.value.removeAttribute('open');
  }
}

onMounted(() => {
  if (open.value) openDialog();
});

onBeforeUnmount(() => {
  closeDialog();
});
</script>

<style scoped lang="scss">
@import '~/styles/adaptive';

$color-white: #FFFFFF;
$color-text-dark: #3A3A3A;
$color-overlay: #FFFFFFCC;
$color-shadow-light: #0000001A;

$gradient-close-btn: linear-gradient(
    177deg,
    rgba(229, 23, 38, 1) 0%,
    rgba(212, 32, 140, 1) 100%
);

.modal {
  color: $color-text-dark;
  border: none;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100svw;
  height: 100svh;
  background: transparent;
  overflow: hidden;

  &::backdrop {
    background: $color-overlay;
  }

  &__body {
    position: relative;
    background: $color-white;
    z-index: 1000;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  &__close {
    top: 0;
    right: 0;
    position: absolute;
    background: transparent;
    border: none;
  }

  @media #{$landscape-media} {
    &::backdrop {
      backdrop-filter: blur(landscape-size(5));
    }

    &__body {
      width: landscape-size(1270);
      height: landscape-size(652);
      border-radius: landscape-size(50);
      box-shadow: 0 0 landscape-size(10) 0 $color-shadow-light;
    }

    &__close {
      top: landscape-size(45);
      right: landscape-size(45);

      &-icon {
        width: landscape-size(30);
        height: landscape-size(30);
      }
    }
  }

  @media #{$portrait-media} {
    &::backdrop {
      backdrop-filter: blur(portrait-size(5));
    }

    &__body {
      width: portrait-size(278);
      height: portrait-size(439);
      border-radius: portrait-size(20);
      box-shadow: 0 0 portrait-size(10) 0 $color-shadow-light;
    }

    &__close {
      top: portrait-size(16);
      right: portrait-size(4);

      &-icon {
        width: portrait-size(22);
        height: portrait-size(22);
      }
    }
  }
}
</style>

