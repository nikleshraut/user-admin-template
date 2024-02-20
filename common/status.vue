<template>
  <!-- Status Modal -->
  <div class="modal fade in" id="confirmStatus" role="dialog" aria-labelledby="confirmStatusLabel">
    <div class="col-sm-6 col-sm-offset-3 modal-dialog-30">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
          <h4 class="modal-title">Change Status</h4>
        </div>
        <div class="modal-body">
          <p>{{ get_status }}</p>
          <input type="hidden" name="currentStatus" id="currentStatus" value="">
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
          <button v-on:click="changeStatus" type="button" class="btn btn-primary" data-dismiss="modal">Ok</button>
        </div>
      </div>
    </div>
  </div>
  <!-- Delete Modal -->
</template>
<script>
  export default {
    mounted() {
      this.status_confirmation=this.$parent.status_confirmation;
    },

    data: function() {
      return {
        status_confirmation: '',
      }
    },
    computed:{
      get_status: function(){
        return this.$parent.status_confirmation;  
      }
      
    },
    methods: {
      changeStatus: function() {
        this.$parent.changeStatus();
      }
    }

  }
</script>


      