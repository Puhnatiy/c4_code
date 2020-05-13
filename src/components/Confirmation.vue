<template>
  <div>
    <b-alert v-model="showDismissibleAlert" variant="success" show dismissible>
    {{ message }}</b-alert>
  </div>
</template>
<script>
/* eslint linebreak-style: ["error", "windows"] */

export default {
  props: ['message', 'showDismissibleAlert'],
};
</script>\n
