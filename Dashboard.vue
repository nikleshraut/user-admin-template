<template>
  <div>
    <div>Dashboard</div>
    <div class="inner-content hide">
      <div v-show="isloading" class="text-center">
          <img src="/images/loading.gif"><br/>Loading...
        </div>
      <div v-if="isLabOwner">
        <!-- Add Pathology -->
        <div class="form-group" v-if="is_pa_domain=='No'">
          <button v-show="!isloading" v-on:click="addLab" type="button" class="btn btn-primary form-control1">Add Pathology</button>
        </div>
        <table v-show="!isloading" id="lab_table" class="table table-striped table-bordered" width="100%" cellspacing="0">
          <thead>
            <tr>
              <th>Lab</th>
              <th>Address</th>
              <th>Contact</th>
              <th>Form Completion</th>
              <th>Status</th>
              <th width="20%">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="lab in labs">
              <td>{{ lab.lab_name }}</td>
              <td>{{ lab.city }}</td>
              <td>{{ lab.owner_contact_no }}</td>
              <td>
                <span class="text-danger" v-if="lab.lab_users==''" v-on:click="completeLabForm(lab.lab_id,true)" role="button">Incomplete Form</span>
                <span class="text-danger" v-if="lab.lab_users!='' && lab.licence==''" v-on:click="completeLabForm(lab.lab_id,false)" role="button">Incomplete Form</span>
                <span class="text-success" v-if="lab.lab_users!='' && lab.licence!=''">Form Completed</span>

              </td>
              <td>
                <span class="text-danger"  v-if="lab.lab_status==0">Inactive</span>
                <span class="text-success" v-if="lab.lab_status==1">Active</span>
              </td>
              <td>
                <button v-on:click="viewLab(lab.lab_id)" class="btn btn-primary" style="padding: 0 10px;">
                <i class="glyphicon glyphicon-note"></i> View </button>

                <button v-if="is_pa_domain=='Yes'" v-on:click="updateLab(lab.lab_id)" class="btn btn-primary" style="padding: 0 10px;">
                <i class="glyphicon glyphicon-note"></i> Edit </button>

                <button v-if="lab.lab_db_name!='' && lab.lab_status==1 && is_pa_domain=='No'" v-on:click="manageLab(lab.lab_host_name)" class="btn btn-primary" style="padding: 0 10px;">
                <i class="glyphicon glyphicon-eye-open"></i> Manage Lab
                </button>
              </td>
            </tr>
          </tbody>
        </table>



      </div>

      <!-- For Customer  -->

      <div v-if="isCustomer">
        <!-- Add Consumer -->
        <div class="form-group"> <button v-show="!isConsumerLoading" type="button" data-toggle="modal" data-target="#add-consumer" class="btn btn-primary form-control1" >Add Consumer</button></div>
        <div v-show="isConsumerLoading" class="text-center">
          <img src="/images/loading.gif"><br/>List of Consumer's
        </div>
        <table v-show="!isConsumerLoading" id="consumer_table" class="table table-striped table-bordered" width="100%" cellspacing="0">
          <thead>
            <tr>
              <th width="35%">Name</th>
              <th width="35%">Email</th>
              <th width="30%">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="consumer in consumers">
              <td>{{ consumer.first_name }} {{ consumer.last_name }}</td>
              <td>{{ consumer.email }}</td>
              <td>
                <button v-on:click="updateConsumer(consumer.id)" data-toggle="modal" data-target="#add-consumer" class="btn btn-primary" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-edit"></i> Edit
                </button>
                <button data-toggle="modal" data-target="#confirmDeleteConsumer" v-on:click="setConsumerToDelete(consumer.id)" class="btn btn-danger" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-trash"></i> Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>

        <!-- Consumer Modal -->
        <div class="modal fade in" id="add-consumer" role="dialog" aria-labelledby="exampleModalLabel" style="display: none; padding-left: 17px;">
          <div class="col-sm-8 col-sm-offset-2 modal-dialog-30">
            <form class="form-body" role="form" v-on:submit.prevent="submitConsumerForm">
              <!-- modal-content -->
              <div class="modal-content">
                <!-- modal-header -->
                <div class="modal-header">
                  <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">×</span></button>
                  <h4 class="modal-title" id="exampleModalLabel">Add/Update Consumer</h4>
                </div>
                <!--/ modal-header -->
                <div v-show="consumerFormLoading" class="text-center">
                  <img src="/images/loading.gif" >
                </div>
                <!-- modal-body -->
                <div v-show="!consumerFormLoading" class="modal-body lab-modal-body">
                  <div class="hide" id="modalErrMsg"></div>
                  <div class="">
                    <div class="row panel">
                      <div class="col-md-9"><h4>Consumer Details</h4></div>
                      <div class="col-md-3 pull-right padding-tb"><span class="required_field"> *</span> denotes required field</div>
                    </div>
                    <div v-bind:class="{'hide':!consumer_form_error}" class="form-group col-sm-12">
                      <div class="text-danger">{{consumer_form_error}}</div>
                    </div>
                    <!-- First Name - Email -->
                    <div class="row">
                      <div v-bind:class="{ 'text-danger': consumer_errors.first_name }" class="col-md-6 form-group name-form-group">
                        <label for="name" class="col-md-4 control-label align-control-label">First Name<span class="required_field">*</span></label>
                        <div class="col-md-8">
                          <input class="form-control" placeholder="First Name" v-model="consumer_form.first_name" type="text" required="">
                          <div v-bind:class="{ 'text-danger': consumer_errors.first_name }">{{consumer_errors.first_name?consumer_errors.first_name[0]:''}}
                          </div>
                        </div>
                      </div>
                      <div v-bind:class="{ 'text-danger': consumer_errors.email }" class="col-md-6 form-group email-form-group">
                        <label for="email" class="col-md-4 control-label align-control-label">Email<span class="required_field">*</span></label>
                        <div class="col-md-8">
                          <input class="form-control" placeholder="Patient Email" v-model="consumer_form.email" value="" type="email" required="">
                          <div v-bind:class="{ 'text-danger': consumer_errors.email }">{{consumer_errors.email?consumer_errors.email[0]:''}}
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
                  <button type="button" v-on:click="resetConsumerForm" class="btn btn-default" data-dismiss="modal">Close</button>
                  <button type="submit" class="btn btn-primary">Submit</button>
                </div>
                <!--/ modal-footer -->
              </div>
              <!--/ modal-content -->
            </form>
          </div>
        </div>
        <!-- Consumer Modal -->
        <!-- Delete Modal -->
        <div class="modal fade in" id="confirmDeleteConsumer" role="dialog" aria-labelledby="exampleModalLabel" style="display: none; padding-left: 17px;">
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
                <button type="button" class="btn btn-danger" data-dismiss="modal" v-on:click="deleteConsumer">Delete</button>
              </div>
            </div>
          </div>
        </div>
        <!-- Delete Modal -->
      </div>



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

      </div>
    </div>
  </template>

  <script type="text/javascript">
    export default {
      mounted() {
        /*all code hide for now dashboard should blank*/
        this.$parent.page_title = "Dashboard";
        this.$parent.help_content = "No help for now";
        this.$parent.back_url = "#/";
        /*this.getAllPathology(false);
        this.getAllConsumer(false);
        this.$parent.lab_id = undefined;
        this.$parent.lab_detail = undefined;
        this.$parent.user_qualifications = undefined;

        this.host_url = window.location.hostname;
        this.current_host=this.host_url.split('.');

        if(this.current_host[1]=="pa"){
          this.is_pa_domain='Yes';
        }*/

      },
      computed:{
        isLabOwner: function(){
          return this.$parent.profile.is_lab_owner;
        },
        isCustomer: function(){
          return this.$parent.profile.is_customer;
        },
        isRefdoctor: function(){
          return this.$parent.profile.is_ref_doctor;
        }
      },
      data: function() {
        return {
          isloading: true,
          isConsumerLoading: true,
          formLoading: false,
          consumerFormLoading: false,
          labs:this.$parent.labs,
          consumers:this.$parent.consumers,


          qualifications:[],
          consumer_form: {},
          consumer_form_error: "",
          consumer_errors: {},
          lab_id_to_delete: "",
          consumer_id_to_delete: "",
          status_info:{'id':'','status':''},
          status_confirmation:'',
          has_lab_users:"",
          is_pa_domain:'No',
          host_url:'',
          current_host:''
        }
      },
      methods: {
        addLab: function(){
         location.href = '#/add-lab';
       },
       resetConsumerForm: function(){
        this.consumer_form= {};
        this.consumer_form_error= "";
        this.consumer_errors= {};
        this.consumer_id_to_delete= "";
      },
      submitConsumerForm: function(){
        this.consumer_errors = {};
        this.consumer_form_error = "";

        this.$http.post('/api/consumers',this.consumer_form).then((response) => {
          //console.log(response.body);
          if(response.body.isvalid){
            if(response.body.success){
              $("#add-consumer").modal("hide");
              this.getAllConsumer(true);
              this.consumer_form = {};
              console.log("successfully updated");
            }else{
              this.consumer_form_error =  response.body.message;
              console.log("error in update");
            }
          }else{
            this.consumer_errors = response.body.errors;
            //console.log(response.body.errors);
          }
        }, (response) => {
          console.log(response);
        });
      },

      updateConsumer: function(consumer_id){
        this.consumerFormLoading = true;
        this.consumer_form = {};
        this.$http.get('/api/consumers/'+consumer_id+'/edit').then((response) => {
          this.consumer_form = response.body.record;
          this.consumer_form.id = consumer_id;
          this.consumerFormLoading = false;
        }, (response) => {
          console.log(response);
        });
      },

      setConsumerToDelete: function(consumer_id){
        this.consumer_id_to_delete = consumer_id;
      },
      setLabToDelete: function(lab_id){
        this.lab_id_to_delete = lab_id;
      },
      deleteConsumer: function(){
        this.$http.delete('/api/consumers/'+this.consumer_id_to_delete).then((response) => {
          if(response.body.success){
            this.getAllConsumer(true);
          }
        }, (response) => {
          console.log(response);
        });
        this.consumer_id_to_delete = "";
      },

      viewLab: function(lab_id){
        location.href = '#/view-lab/'+lab_id;
       /*this.formLoading = true;
        this.lab_form = {};
        this.$http.get('/api/pathology/'+lab_id).then((response) => {

          this.$parent.lab_detail = response.body.lab_detail;
          location.href = '#/view-lab';
        }, (response) => {
          console.log(response);
        });*/
      },

      manageLab: function(lab_domain){
        var host_url = window.location.hostname;
        var current_host1=host_url.split('.');
        window.open(
          'http://'+lab_domain+'.pa.'+current_host1[0]+'.'+current_host1[1]+'/login',
          '_blank' // <- This is what makes it open in a new window.
        );
     },
      getAllConsumer: function(destroy=false){
        if(!this.consumers.length || destroy){
          this.$http.get('/api/consumers').then((response) => {
            this.consumers = response.body.records;
            this.$parent.consumers = this.consumers;
            this.isConsumerLoading = false;
            if(destroy){
              $('#consumer_table').DataTable().destroy();
            }
            setTimeout(function () {
              $('#consumer_table').DataTable({
                "aLengthMenu": [[5, 10, -1], [5, 10, "All"]],
                "iDisplayLength": 5
              });
            }, 100);
          }, (response) => {
            console.log(response);
          });
        }else{
          this.isConsumerLoading = false;
          $('#consumer_table').DataTable({
            "aLengthMenu": [[5, 10, -1], [5, 10, "All"]],
            "iDisplayLength": 5
          });
        }
      },
      getAllPathology: function(destroy=false){
        //console.log(this.$parent.labs);
        if(!this.labs.length || destroy){
          this.$http.get('/api/pathologies').then((response) => {
            this.labs = response.body.records;
            this.qualifications= response.body.qualifications;
            this.$parent.labs = this.labs;
            //console.log(this.$parent.labs);
            this.isloading = false;
            if(destroy){
              $('#lab_table').DataTable().destroy();
            }
            setTimeout(function () {
              $('#lab_table').DataTable({
                "aLengthMenu": [[5, 10, -1], [5, 10, "All"]],
                "iDisplayLength": 5
              });
            }, 100);

          }, (response) => {
            console.log(response);
          });
        }else{
          this.isloading = false;
          $('#lab_table').DataTable({
            "aLengthMenu": [[5, 10, -1], [5, 10, "All"]],
            "iDisplayLength": 5
          });
        }
      },
      completeLabForm: function(lab_id,has_lab_users){
        //console.log(lab_id+":"+has_lab_users);
        this.$parent.lab_id = lab_id;
        this.$parent.lab_detail = undefined;
        if(has_lab_users){
          location.href = '#/add-lab-user';
        }else{
          location.href = '#/add-licence';
        }
      }

    }
  }
</script>
