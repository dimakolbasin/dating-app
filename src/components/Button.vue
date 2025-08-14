<template>
  <button
    :type="type || 'button'"
    class="button"
    :class="variantClass"
    :disabled="disabled"
  >
    <span class="button__content">
      <slot />
    </span>
  </button>
</template>

<script setup lang="ts">
import { computed } from 'vue';

interface Props {
  variant?: 'primary';
  type?: 'button' | 'submit' | 'reset';
  disabled?: boolean;
}

const props = defineProps<Props>();

const variantClass = computed(() => (props.variant ? [`button--${props.variant}`] : []));
</script>

<style scoped lang="scss">
@import '~/styles/adaptive';

$btn-color-text: #fff;
$btn-gradient-primary: linear-gradient(177deg, rgba(229, 23, 38, 1) 0%, rgba(212, 32, 140, 1) 100%);
$btn-font-weight-semibold: 600;

.button {
  font-weight: $btn-font-weight-semibold;
  font-size: var(--button-font-size);
  line-height: var(--button-line-height);
  letter-spacing: var(--letter-spacing-button);
  color: $btn-color-text;
  background: $btn-gradient-primary;
  border: none;
  cursor: pointer;
  outline: none;

  &__content {
    text-transform: uppercase;
  }

  &--primary {
    background: $btn-gradient-primary;
    color: $btn-color-text;
  }

  &:hover:not([disabled]) {
    filter: brightness(1.05);
  }

  &:active:not([disabled]) {
    filter: brightness(0.95);
  }

  &[disabled] {
    opacity: 0.6;
    cursor: not-allowed;
  }

  @media #{$landscape-media} {
    width: landscape-size(381);
    height: landscape-size(80);
    border-radius: landscape-size(10);

    &__content {
      @include font-responsive(landscape-size(23), 100%);
    }
  }

  @media #{$portrait-media} {
    width: portrait-size(171);
    height: portrait-size(40);
    border-radius: portrait-size(6);

    &__content {
      @include font-responsive(portrait-size(13), 100%);
    }
  }
}
</style>