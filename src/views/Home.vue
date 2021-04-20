<template>
  <div class="home">
    <app/>
  </div>
</template>

<script>
// @ is an alias to /src
import app from "@/components/HelloWorld.vue";

export default {
  name: "Home",
  components: {
    app,
  },
};
</script>
