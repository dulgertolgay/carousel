<template>
  <div class="card">
    <p>{{ msg }}</p>
  </div>
</template>

<script>
export default {
  props: ["msg"],
};
</script>

<style scoped>
div {
  width: 250px;
  margin-right: 10px;
  display: inline-flex;
  height: 125px;
  background-color: #39b1bd;
  color: white;
  border-radius: 4px;
  align-items: center;
  justify-content: center;
}
</style>
