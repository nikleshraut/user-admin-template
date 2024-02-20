<template>
  <div>
  <div class="inner-content">
      <!-- Add Record -->
      <div class="form-group">
        <button v-on:click="updateRecord(0)" type="button" data-toggle="modal" data-target="#add-edit-record" class="btn btn-primary form-control1">Add Permission</button>
      </div>

      <div v-show="isloading" class="text-center">
        <img src="/images/loading.gif"><br/>Permissions
      </div>

      <div v-show="div_success" class="alert alert-success row text-center col-sm-12 " id="div_success" >
        <div class="text-center" style="display: table;">
        <a class="close" v-on:click="removeMsg" style="position: absolute;top: -15px;right: 0;">×</a>
        <label id="lbl_success">{{ lbl_success }} </label>
        </div>
      </div>

      <div v-show="div_error" class="alert alert-danger row text-center col-sm-12 " id="div_error" >
        <div class="text-center" style="display: table;">
        <a class="close" v-on:click="removeMsg" style="position: absolute;top: -15px;right: 0;">×</a>
        <label id="lbl_error">{{ lbl_error }} </label>
        </div>
      </div>

      <table v-show="!isloading" id="record_table" class="table table-striped table-bordered" width="100%" cellspacing="0">
        <thead>
          <tr>
            <th>Name</th>
            <th>Display Name</th>
            <th>Description</th>
            <th>Roles</th>
            <th>Status</th>
            <th width="20%">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="record in records">
            <td>{{ record.name }}</td>
            <td>{{ record.display_name }}</td>
            <td>{{ record.description }}</td>
            <td>{{ record.permission_roles}}</td>
            <td>
                <a href="#" v-if="record.permission_status==0" @click.prevent="updateStatus(record.id,0)"><span class="text-danger">Inactive</span></a>
                <a href="#" v-if="record.permission_status==1"  @click.prevent="updateStatus(record.id,1)"><span class="text-success">Active</span></a>                  
            </td>
            <td>
              <button v-on:click="updateRecord(record.id)" data-toggle="modal" data-target="#add-edit-record" class="btn btn-primary" style="padding: 0 10px;">
                <i class="glyphicon glyphicon-edit"></i> Edit
              </button>
              <button  data-toggle="modal" data-target="#confirmDelete" v-on:click="updateRecord(record.id)" class="btn btn-danger" style="padding: 0 10px;">
                <i class="glyphicon glyphicon-trash"></i> Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>


      <!-- Add-Edit Modal -->
      <div class="modal fade in" id="add-edit-record" role="dialog" aria-labelledby="exampleModalLabel" style="display: none; padding-left: 17px;">
        <div class="col-sm-8 col-sm-offset-2 modal-dialog-30">

          <form class="form-body" role="form" method="POST" v-on:submit.prevent="submitForm">

            <!-- modal-content -->
            <div class="modal-content">

              <!-- modal-header -->
              <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">×</span></button>
                <h4 class="modal-title" id="exampleModalLabel">Add/Update Permission</h4>
              </div>
              <!--/ modal-header -->

              <div v-show="formLoading" class="text-center">
                <img src="/images/loading.gif" style="">
              </div>

              <!-- modal-body -->
              <div v-show="!formLoading" class="modal-body lab-modal-body">
                <div class="hide" id="modalErrMsg"></div>
                <div class="">
                  <div class="row panel">
                    <div class="col-md-9"><h4>Permission</h4></div>
                    <div class="col-md-3 pull-right padding-tb"><span class="required_field"> *</span> denotes required field</div>
                  </div>

                  <!-- Name - Display Name -->
                  <div class="row">
                    <div class="col-md-6 form-group name-form-group">
                      <label for="name" class="col-md-4 control-label align-control-label">Name<span class="required_field">*</span></label>
                      <div class="col-md-8">
                        <input class="form-control" placeholder="Name" v-model="data_form.name" type="text" required>
                        <div class="name-error">
                        </div>
                      </div>
                    </div>
                    <div class="col-md-6 form-group display_name-form-group">
                      <label for="display_name" class="col-md-4 control-label align-control-label">Display Name<span class="required_field">*</span></label>
                      <div class="col-md-8">
                        <input class="form-control" placeholder="Display Name" v-model="data_form.display_name" value="" type="text" required>
                        <div class="display_name-error">
                        </div>
                      </div>
                    </div>
                  </div>

                  <!-- Description -->
                  <div class="row">
                    <div class="col-md-12 form-group description-form-group">
                      <label for="description" class="col-md-2 control-label align-control-label">Description</label>
                      <div class="col-md-10">
                        <input class="form-control" placeholder="Description" v-model="data_form.description" type="text">
                        <div class="name-error">
                        </div>
                      </div>
                    </div>

                  <!-- Status -->
                  <div class="row">
                    <div class="col-md-12 form-group status-form-group">
                      <label for="status" class="col-md-2 control-label align-control-label">Status</label>
                      <div class="col-md-10">
                          <input type="radio" name="record_status" value="1" v-bind:value="1" v-model="data_form.permission_status"> &nbsp;Active &nbsp;
                          <input type="radio" name="record_status" value="0" v-bind:value="0" v-model="data_form.permission_status" required> &nbsp;Inactive&nbsp;

                        <div class="status-error">
                        </div>
                      </div>
                    </div>
                  </div>

                </div>
              </div>
              <!--/ modal-body -->

              <!-- modal-footer -->
              <div class="modal-footer">
                <input id="record_id" name="record_id" value="" type="hidden">
                <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary">Submit</button>
              </div>
              <!--/ modal-footer -->

            </div>
            <!--/ modal-content -->
          </form>
        </div>
      </div>


      <!-- Delete Modal -->
      <div class="modal fade in" id="confirmDelete" role="dialog" aria-labelledby="exampleModalLabel" style="display: none; padding-left: 17px;">
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
              <button type="button" class="btn btn-danger" data-dismiss="modal" v-on:click="deleteRecord">Delete</button>
            </div>
          </div>
        </div>
      </div>
      <!-- Delete Modal -->


      <!-- Modal Dialog -->
      <div class="modal fade" id="confirmStatus" role="dialog" aria-labelledby="confirmStatusLabel" aria-hidden="true">
        <div class="col-sm-6 col-sm-offset-3 modal-dialog-30">
          <div class="modal-content">
            <div class="modal-header">
              <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
              <h4 class="modal-title">Change Status</h4>
            </div>
            <div class="modal-body">
              <p id="status_message">{{ status_confirmation }}   </p>
              <input type="hidden" name="currentStatus" id="currentStatus" value="">
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
              <button type="button" class="btn btn-primary" id="confirm" @click.prevent="changeStatus()">Ok</button>
            </div>
          </div>
        </div>
      </div>
      <!-- Modal Dialog -->

    </div>
  </div>
</template>

  <script type="text/javascript">
    export default {
      
      mounted() {
        this.$parent.page_title = "Permissions";
        this.getRecords(false);
      },

      data: function() {
        return {
          isloading: true,
          formLoading: false,
          records:[],
          data_form:{},
          div_success:false,
          lbl_success:'',
          div_error:false,
          lbl_error:'',
          status_info:{'id':'','status':''},
          status_confirmation:''        

        }
      },

      methods: {
        getRecords: function(destroy=false){
          this.$http.get('/api/permissions/').then((response) => {
          this.records = response.body.records;
          this.isloading = false;
          if(destroy){
            $('#record_table').DataTable().destroy();
          }
          setTimeout(function () {
            $('#record_table').DataTable({
              "aLengthMenu": [[5, 10, -1], [5, 10, "All"]],
              "iDisplayLength": 5
            });
          }, 100);

        }, (response) => {
          console.log(response);
        });
      },
      
        

       resetForm: function(){
        this.data_form= {};
        this.data_form_error= "";
      },
 
     

      updateRecord: function(rec_id){
        this.formLoading = false;
        this.data_form = {};
        if(rec_id!="0"){
          this.$http.get('/api/permission/'+rec_id).then((response) => {
            this.data_form = response.body.record;
            this.data_form.id = rec_id;
            this.formLoading = false;

          }, (response) => {
            console.log(response);
          });

        }
      },
    
      submitForm: function(){
        this.$http.post('/api/permissions',this.data_form).then((response) => {
          if(response.body.success){
            $("#add-edit-record").modal("hide");
            this.getRecords(true);
            this.data_form = {};
            console.log("successfully updated");
            this.div_success=true;
            this.lbl_success="Permission successfully updated.";

          }else{
            console.log("error in update");
            this.div_success=false;
            this.div_error=true;
            this.lbl_error=response.body.message;
            
          }
        }, (response) => {
          console.log(response);
            this.div_error=true;
            this.lbl_error=response.body.message;

        });
      },

      deleteRecord: function(){
        //this.data_form.id = rec_id;
        this.$http.delete('/api/permission/'+this.data_form.id).then((response) => {
          if(response.body.success){
            this.div_success=true;
            this.lbl_success="Permission deleted successfully.";
            this.getRecords(true);
          }else{
            this.div_error=true;
            this.lbl_error=response.body.message;
          }
        }, (response) => {
            
            this.div_error=true;
            this.lbl_error=response.body.message;
        });
        
      },

      updateStatus:function(vId, vStatus){
        this.status_info.id=vId;
        this.status_info.status=vStatus;
        var nStatus=(vStatus==0)?'active':'inactvie';
        this.status_confirmation= 'Are you sure to change status as '+nStatus+' ?';
        $("#confirmStatus").modal("show");

      },

      changeStatus: function(){

        var input = this.status_info;
        this.$http.post('/api/permission_status',input).then((response) => {
          this.getRecords(true);
          this.status_info = {'id':'','status':''};
          $("#confirmStatus").modal('hide');
          this.div_success=true;
          this.lbl_success="Status udpated successfully.";

        }, (response) => {

        });

      },
      
      removeMsg:function(){
        this.div_success=false;
        this.div_error=false;
      }

    

    
    }
  }
</script>
