<template>
  <div class="display-screen">
    {{ currentValue }}
  </div>
</template>

<script>

export default {
  name: 'DisplayScreen',
  props: {
    currentValue: String
  }
}
</script>

<style>
  .display-screen {
    display: flex;
    justify-content: flex-end;
    align-items: flex-end;
    font-size: 32px;
    flex: 1;
  }
</style>
