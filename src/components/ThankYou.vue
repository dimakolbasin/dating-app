<template>
  <div class="thank-you" role="status" aria-live="polite">
    <h3 class="thank-you__title">Thank You</h3>
    <p class="thank-you__text">To complete registration, please check your e-mail</p>
  </div>
</template>

<script setup lang="ts">
</script>

<style scoped lang="scss">
@import '~/styles/adaptive';

$color-text-dark: #3A3A3A;
$gradient-text-primary: linear-gradient(
    177deg,
    rgba(229, 23, 38, 1) 0%,
    rgba(212, 32, 140, 1) 100%
);
$font-weight-bold: 700;
$font-weight-extra-bold: 800;

.thank-you {
  text-align: center;
  display: flex;
  flex-direction: column;

  h3,
  p {
    margin: 0;
    padding: 0;
  }

  &__title {
    font-weight: $font-weight-extra-bold;
    background: $gradient-text-primary;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  &__text {
    font-weight: $font-weight-bold;
    color: $color-text-dark;
    white-space: normal;
  }

  @media #{$landscape-media} {
    padding-bottom: landscape-size(90);

    &__title {
      @include font-responsive(landscape-size(77), 160%);
      letter-spacing: landscape-size-neg(-4);
    }

    &__text {
      @include font-responsive(landscape-size(28), 136%);
      letter-spacing: landscape-size-neg(-1.5);
    }
  }

  @media #{$portrait-media} {
    padding: 0 portrait-size(40);
    padding-bottom: portrait-size(10);

    &__title {
      @include font-responsive(portrait-size(35), 180%);
      letter-spacing: portrait-size-neg(-1);
    }

    &__text {
      @include font-responsive(portrait-size(14), 140%);
      letter-spacing: portrait-size-neg(-0.5);
    }
  }
}
</style>
