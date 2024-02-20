<template>
 <div style="border:1px solid blue;">
    <p> <a href="#menu-toggle" role="button" id="menu-toggle">Toggle </a>This Content Area</p>
  </div>
</template>
<script>
  export default {
    mounted() {
        console.log("home vue");
        this.$parent.page_title = "Home";
    }
  }
</script>
