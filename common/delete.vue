<template>
  <!-- Delete Modal -->
  <div class="modal fade in" id="confirmDelete" role="dialog" aria-labelledby="exampleModalLabel">
    <div class="col-sm-6 col-sm-offset-3 modal-dialog-30">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
          <h4 class="modal-title">Delete Parmanently</h4>
        </div>
        <div class="modal-body">
          <p>Are you sure about this ?</p>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
          <button v-on:click="deleteRecord" type="button" class="btn btn-danger" data-dismiss="modal">Delete</button>
        </div>
      </div>
    </div>
  </div>
  <!-- Delete Modal -->
</template>
<script>
  export default {

    methods: {
      deleteRecord: function() {
        this.$parent.deleteRecord();
      }
    }

  }
</script>