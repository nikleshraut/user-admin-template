<template>
  <!-- Authorised Div -->
    <div class="col-sm-6 col-sm-offset-3 modal-dialog-30 "  >
      <div class="modal-content" >
        <div class="modal-header">
          <h4 class="modal-title">Unauthorised Access</h4>
        </div>
        <div class="modal-body">
          <p>You don't have rights to access this page.</p>
        </div>
        <div class="modal-footer">
          <button v-on:click="backPage" type="button" class="btn btn-default" >Back to Home</button>
        </div>
      </div>
    </div>
  <!-- Authorised Div -->
</template>
<script>
  export default {

    methods: {
      backPage: function() {
        location.href = '#/home';
      }
    }

  }
</script>