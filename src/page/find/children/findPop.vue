<template>
    <find-content></find-content>
</template>

<script>
import findContent from "./findContent";
export default {
  name: "findPop",
  components: {
    findContent
  },
  mounted() {
    this.$children[0].getUsers();
  }
};
</script>

<style lang='less'>
</style>
