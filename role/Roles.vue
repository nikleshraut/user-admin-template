<template>
  <div>
  <div class="inner-content"  v-if="showPage">
      <!-- Add Record -->
      <div class="form-group">
        <!-- <button v-on:click="updateRecord(0)" type="button" data-toggle="modal" data-target="#add-edit-record" class="btn btn-primary form-control1">Add Role</button> -->
      </div>

      <div v-show="isloading" class="text-center">
        <img src="/images/loading.gif"><br/>Roles
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
            <th>Permissions</th>
            <th>Status</th>
            <th width="20%">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="record in records">
            <td>{{ record.name }}</td>
            <td>{{ record.display_name }}</td>
            <td>{{ record.description }}</td>
            <td>{{ record.role_permissions }}</td>
            <td>
                <a href="#" v-if="record.role_status==0" @click.prevent="updateStatus(record.id,0)"><span class="text-danger">Inactive</span></a>
                <a href="#" v-if="record.role_status==1"  @click.prevent="updateStatus(record.id,1)"><span class="text-success">Active</span></a>
            </td>
            <td>
              <button v-on:click="updateRecord(record.id)" data-toggle="modal" data-target="#add-edit-record" class="btn btn-primary" style="padding: 0 10px;">
                <i class="glyphicon glyphicon-edit"></i> Edit
              </button>
              <!-- <button  data-toggle="modal" data-target="#confirmDelete" v-on:click="updateRecord(record.id)" class="btn btn-danger" style="padding: 0 10px;">
                <i class="glyphicon glyphicon-trash"></i> Delete
              </button> -->
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
                <h4 class="modal-title" id="exampleModalLabel">Add/Update Role</h4>
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
                    <div class="col-md-9"><h4>Role</h4></div>
                    <div class="col-md-3 pull-right padding-tb"><span class="required_field"> *</span> denotes required field</div>
                  </div>

                  <!-- Name - Display Name -->
                  <div class="row">
                    <div class="col-md-6 form-group name-form-group">
                      <label for="name" class="col-md-4 control-label align-control-label">Name</label>
                      <div class="col-md-8">
                        <input class="form-control" placeholder="Name" v-model="data_form.name" type="text" disabled="disabled" >
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
                  </div>

                  <!-- Status -->
                  <div class="row">
                    <div class="col-md-12 form-group status-form-group">
                      <label for="status" class="col-md-2 control-label align-control-label">Status</label>
                      <div class="col-md-10">
                          <input type="radio" name="record_status" value="1" v-bind:value="1" v-model="data_form.role_status"> &nbsp;Active &nbsp;
                          <input type="radio" name="record_status" value="0" v-bind:value="0" v-model="data_form.role_status" required> &nbsp;Inactive&nbsp;

                        <div class="status-error">
                        </div>
                      </div>
                    </div>
                  </div>

                <div class="row" style="border-bottom: 1px solid #e5e5e5;">
                    <div class="col-md-2 modal-title " style="padding:15px;" >
                        <h4 class="modal-title">Permissions</h4>
                    </div>
                    <div class="col-md-4 checkbox " style="padding:10px 10px 10px 15px;" >
                        <label><input type="checkbox" id="checkAllCheckbox" v-on:click="selectAll" v-model="checkAll"  /> Check all</label>
                    </div>
                </div>
               <div style="height:200px;overflow-y:scroll;overflow-x:hidden;">

                      <div v-for="permission in permissionList" class="col-md-4 modal-title " style="padding:0px 5px 0px 15px;" >
                        <div class="checkbox">
                            <label>
                                <input name="checkedNames[]" type="checkbox" v-bind:id="permission.id" class="user-permissions" v-bind:value="permission.id" > {{ permission.name }}
                            </label>
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
     {{ ValidPageAccess }}
    <unauthorised-vue v-if="!showPage"></unauthorised-vue>

  </div>
</template>

  <script type="text/javascript">
  import unauthorisedVue from '../common/unauthorised.vue';

    export default {

      mounted() {
      this.$parent.showAppPage=false;
      this.$parent.page_title = "";

/*
        this.$parent.page_title = "Roles";
        this.getRecords(false);
*/
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

          permissionList:{},
          checkedPermissions:[],
          checkAll:false,

          status_info:{'id':'','status':''},
          status_confirmation:'',

          showPage:false,
          valid_access:false,

        }
      },

    computed: {
      ValidPageAccess: function () {
        this.valid_access=this.$parent.valid_access;
        this.checkAccess();
      }
    },
    components: {

      'unauthorisedVue':unauthorisedVue
    },

      methods: {

        getRecords: function(destroy=false){
          this.$http.get('/api/roles/').then((response) => {
            this.records = response.body.records;
            this.permissionList=response.body.permissionList;
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
        $("input[name='checkedNames[]']").prop("checked",false);
      },

      selectAll: function(){

          if(this.checkAll==false){
            $("input[name='checkedNames[]']").prop("checked",true);
            $("input[name='checkedNames[]']").attr("checked","checked");
          }else{
            $("input[name='checkedNames[]']").prop("checked",false);
            $("input[name='checkedNames[]']").removeAttr("checked","checked");

          }
      },


      updateRecord: function(rec_id){
        this.formLoading = false;
        this.data_form = {};
        this.resetForm();
        if(rec_id!="0"){
          this.$http.get('/api/role/'+rec_id).then((response) => {
            this.data_form = response.body.record;
            this.checkedPermissions= response.body.checkedPermissions;
            this.data_form.id = rec_id;
            this.formLoading = false;
            var checkedPermissions = this.checkedPermissions;
            console.log(checkedPermissions);

            $(".user-permissions").each(function(){
                if($.inArray(parseInt($(this).val()),checkedPermissions)!=-1){
                  $(this).prop("checked",true);
                  $(this).attr("checked","checked");
                }
            });

          }, (response) => {
            console.log(response);
          });

        }
      },

      submitForm: function(){


        var checkedItems = $("input[name='checkedNames[]']:checked");
        var test=[];
        $.map(checkedItems, function(val, key) {
          test.push(val.value);
        });
       this.data_form.permission_id=test;


        console.log(this.data_form.permission_id);
        //var x = $("#permissionId").serializeArray();

        this.$http.post('/api/roles',this.data_form).then((response) => {
          if(response.body.success){
            $("#add-edit-record").modal("hide");
            this.getRecords(true);
            this.data_form = {};
            console.log("successfully updated");
            this.div_success=true;
            this.lbl_success="Role successfully updated.";

          }else{
            console.log("error in update");
            this.div_success=false;
            this.div_error=true;
            this.lbl_error=response.body.message;

          }
        }, (response) => {
            this.div_error=true;
            this.lbl_error=response.body.message;
        });
      },

        deleteRecord: function(){
        //this.data_form.id = rec_id;
        this.$http.delete('/api/role/'+this.data_form.id).then((response) => {
          if(response.body.success){
            this.div_success=true;
            this.lbl_success="Role deleted successfully.";

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
        this.$http.post('/api/role_status',input).then((response) => {
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
      },

      checkAccess: function(){

          if(this.valid_access==''){
            this.$parent.validAccess();
          }

          if(this.valid_access==true ){
            // To restrict access this page at pathonet.dev
            if(this.$parent.app_host.main_domain==this.$parent.app_domains.server_domain){
                this.$parent.valid_access=false;
                this.valid_access=false;
            }
          }
          if(this.valid_access==true ){
            this.showloader =  false;
            this.$parent.page_title = "Roles";
            this.$parent.showAppPage=true;
            this.showPage=true;
            this.getRecords();

            /*
                    this.$parent.page_title = "Roles";
                    this.getRecords(false);
            */

          }
       }



    }

  }
</script>
