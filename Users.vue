<template>
  <div>
    <div class="content-title">
      <h3 class="main-title">Users 1</h3>
    </div>
    <div class="inner-content">
      <div class="panel theme-panel">
        <ul >
          <li v-for="user in users">
            {{ user.name }}
          </li>          
      </div>
    </div>
  </div>
</template>
<script>
   

  export default {
    data() {
      return {
        
        users: {}

      }
    },

    mounted() {
      
      this.$http.get('/api/user').then((response) => {
          this.users=response.body.records;
        }, (response) => {

      }); 

    } 
  }
</script>
