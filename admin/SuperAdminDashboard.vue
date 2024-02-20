<template>
  <div>
    <div class="inner-content">

        <div v-show="isloading" class="text-center">
          <img src="/images/loading.gif"><br/>List of Pathology
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

        <table v-show="!isloading" id="lab_table" class="table table-striped table-bordered" width="100%" cellspacing="0">
          <thead>
            <tr>
              <th>Lab</th>
              <th>Lab-Admin</th>
              <th>Owner</th>
              <th>Address</th>
              <th>Contact</th>
              <th>Status</th>
              <th width="20%">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="lab in labs">
              <td><b>{{ lab.lab_name }}</b><br> {{ lab.lab_host_name }}</td>
              <td><b>{{ lab.first_name }} {{ lab.first_name }} </b><br>{{ lab.email }}</td>
              <td><b> {{ lab.owner_firstname }} {{ lab.owner_lastname }} </b><br>
                      {{ lab.owner_designation }}<br>
                      {{ lab.owner_qualification }}<br>
                      {{ lab.owner_reg_no }}<br>
              </td>
              <td>
                  <span v-if="lab.address_ward!=''">{{ lab.address_ward }}<br></span>
                  <span v-if="lab.address_mohalla!=''">{{ lab.address_mohalla }}<br></span>
                  <span v-if="lab.address_lane!=''">{{ lab.address_lane }}<br></span>
                  <span v-if="lab.address_area!=''">{{ lab.address_area }}<br></span>
                  <span >{{ lab.city }} - {{ lab.district }}</span>
                </td>
              <td>{{ lab.lab_phone }}<br>{{ lab.owner_contact_no }}</td>
              
              <td>
                  <a href="#" v-if="lab.lab_status==0" @click.prevent="labStatus(lab.lab_id,0)">Inactive</a>
                  <a href="#" v-if="lab.lab_status==1"  @click.prevent="labStatus(lab.lab_id,1)">Active</a>
              </td>
              <td>
                <button v-on:click="updateLab(lab.lab_id)" class="btn btn-primary" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-edit"></i> Edit
                </button>
                <button v-if="!lab.lab_status" data-toggle="modal" data-target="#confirmDelete" v-on:click="setLabToDelete(lab.lab_id)" class="btn btn-danger" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-trash"></i> Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
         

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
                <button type="button" class="btn btn-danger" data-dismiss="modal" v-on:click="deletelab">Delete</button>
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
      
      </div>
    </div>
  </template>

  <script type="text/javascript">
    export default {
      mounted() {
        this.$parent.page_title = "Super Admin Dashboard";
        this.getAllPathology(false);
        this.$parent.lab_id = undefined;
        this.$parent.lab_detail = undefined;

      },
      data: function() {
        return {
          isloading: true,
          labs:[],
          
          lab_id_to_delete: "",
          status_info:{'id':'','status':''},
          status_confirmation:'',
          has_lab_users:"",

          div_success:false,
          lbl_success:'',
          div_error:false,
          lbl_error:''




        }
      },
      methods: {
        addLab: function(){
         location.href = '#/add-lab';
       },

      setLabToDelete: function(lab_id){
        this.lab_id_to_delete = lab_id;
      },
  
      deletelab: function(){
        this.$http.delete('/api/pathology/'+this.lab_id_to_delete).then((response) => {
          if(response.body.success){
            this.getAllPathology(true);
            this.div_success=true;
            this.lbl_success="Role successfully updated.";            
          }
        }, (response) => {
          console.log(response);
            this.div_success=false;
            this.div_error=true;
            this.lbl_error=response.body.message;          
        });
        this.lab_id_to_delete = "";
      },
    
      getAllPathology: function(destroy=false){
          this.$http.get('/api/pathologies').then((response) => {
          this.labs = response.body.records;
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
      },
  
      updateLab: function(lab_id){
      this.formLoading = true;
      this.lab_form = {};
      this.$http.get('/api/pathology/'+lab_id).then((response) => {

        this.$parent.lab_detail = response.body.lab_detail;
        this.lbl_success='';
        this.lbl_error='';
        location.href = '#/edit-lab';
      }, (response) => {
        console.log(response);
      });
    },


      labStatus:function(vId, vStatus){
        this.status_info.id=vId;
        this.status_info.status=vStatus;
        var nStatus=(vStatus==0)?'active':'inactvie';
        this.status_confirmation= 'Are you sure to change status as '+nStatus+' ?';
        $("#confirmStatus").modal("show");

      },

      changeStatus: function(){

        var input = this.status_info;
        this.$http.post('/api/lab_status',input).then((response) => {
          this.getAllPathology(true);
          this.status_info = {'id':'','status':''};
          $("#confirmStatus").modal('hide');
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
