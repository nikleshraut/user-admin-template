<template>
  <div>
    <div class="padding" >
      <div class="box-header">
        <!-- / .modal -->
        <div>
          <div v-show="showloader" class="text-center loader">
            <img src="/images/loading.gif">
          </div>

          <div class="table-responsive">
            <div v-show="error" class="col-sm-12">
              <div v-bind:class="error.type" class="text-center">
                {{error.message}}
              </div>
            </div>
            <div class="col-sm-12 form-group">
              <button v-on:click="updateRecord(0)" data-toggle="modal" data-target="#add-edit-record" class="row btn btn-primary btn-sm pull-right">Add new test report</button>
            </div>
            <table class="table table-striped b-t b-b list-table">
              <thead>
                <tr>
                  <th>Test Report</th>
                  <th>Group</th>
                  <th>Unit</th>
                  <th>Status</th>
                  <th width="20%">Action</th>                  
                </tr>
              </thead>
              <tbody>
                <tr v-for="(record,index) in test_reports">
                  <td class="table-td">{{ record.name }} </td>
                  <td class="table-td">{{ record.test_group }}</td>
                  <td class="table-td">
                    <a href="#" v-if="record.status==0" @click.prevent="updateStatus(record.id,0)"><span class="text-danger">Inactive</span></a>
                    <a href="#" v-if="record.status==1"  @click.prevent="updateStatus(record.id,1)"><span class="text-success">Active</span></a>                  
                  </td>
                  <td class="table-td">
                    <button v-on:click="updateRecord(record.id)" data-toggle="modal" data-target="#add-edit-record" class="btn btn-primary" style="padding: 0 10px;">
                      <i class="glyphicon glyphicon-edit"></i> Edit
                    </button>

                    <button data-toggle="modal" data-target="#confirmDelete" v-on:click="setRecordToDelete(record.id)" class="btn btn-danger" style="padding: 0 10px;">
                      <i class="glyphicon glyphicon-trash"></i> Delete 
                    </button>

                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- Add-Edit Modal -->
          <div class="modal fade in" id="add-edit-record" role="dialog" aria-labelledby="exampleModalLabel" style="display: none; padding-left: 17px;">
            <div class="col-sm-9 col-sm-offset-2 modal-dialog-30">

              <form class="form-body" role="form" method="POST" v-on:submit.prevent="submitForm">

                <!-- modal-content -->
                <div class="modal-content">

                  <!-- modal-header -->
                  <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">×</span></button>
                    <h4 class="modal-title" id="exampleModalLabel">Add/Update Test Report</h4>
                  </div>
                  <!--/ modal-header -->

                  <!-- modal-body -->
                  <div class="modal-body lab-modal-body">
                    <div class="hide" id="modalErrMsg"></div>
                    <div class="">
                      <div class="row panel">
                        <div class="col-md-9"><h4>Test Report</h4></div>
                        <div class="col-md-3 pull-right padding-tb"><span class="required_field"> *</span> denotes required field</div>
                      </div>

                      <!-- Group -->
                      <div class="row">
                        <div class="col-md-12 form-group test_group-form-group">
                          <label for="test_group" class="col-md-2 control-label align-control-label">Group<span class="required_field">*</span></label>
                          <div class="col-md-10">
                            <div class="col-md-2">BIOCHEM : <input name="test_group" id="groupBIOCHEM" value="BIOCHEM" type="radio" v-model="data_form.test_group" required=""  v-on:change="callGroupAttribute"></div>
                            <div class="col-md-3">Hematology : <input name="test_group" id="groupHematology" value="Hematology" type="radio" v-model="data_form.test_group" v-on:change="callGroupAttribute"></div>
                            <div class="col-md-2">ECI : <input name="test_group" id="groupECI" value="ECI" type="radio" v-model="data_form.test_group"  v-on:change="callGroupAttribute"></div>
                            <div class="col-md-2">Rochi : <input name="test_group" id="groupRochi" value="Rochi" type="radio" v-model="data_form.test_group" v-on:change="callGroupAttribute"></div>
                            <div class="col-md-3">Clinical Path : <input name="test_group" id="groupClinicalPath" value="Clinical Path" type="radio" v-model="data_form.test_group" v-on:change="callGroupAttribute"></div>

                            <div class="name-error"></div>

                          </div>
                        </div>
                      </div>

                      <!-- Test Method -->
                      <div class="row">
                        <div class="col-md-12 form-group test_method-form-group">
                          <label for="test_method" class="col-md-2 control-label align-control-label">Group<span class="required_field">*</span></label>
                          <div class="col-md-10">
                            <div class="col-md-2">NABL : <input name="test_method" id="methodNABL" value="NABL" type="radio" v-model="data_form.test_method" required=""></div>
                            <div class="col-md-8">Other : <input name="test_method" id="methodOther" value="Other" type="radio" v-model="data_form.test_method"></div>
                            <div class="name-error"></div>
                          </div>
                        </div>
                      </div>    

                      <!-- Test Report -->
                      <div class="row">
                        <div class="col-md-12 form-group name-form-group">
                          <label for="name" class="col-md-2 control-label align-control-label">Test Report<span class="required_field">*</span></label>
                          <div class="col-md-10">
                            <input class="form-control" placeholder="Test Report" v-model="data_form.name" type="text" required>
                            <div class="name-error"></div>
                          </div>
                        </div>
                      </div>    

                      <!-- Short Description -->
                      <div class="row">
                        <div class="col-md-12 form-group short_description-form-group">
                          <label for="short_description" class="col-md-2 control-label align-control-label">Short Description</label>
                          <div class="col-md-10">
                            <input class="form-control" placeholder="Short Description" v-model="data_form.short_description" type="text" >
                            <div class="short_description-error"></div>
                          </div>
                        </div>
                      </div>
                      
                      <!-- Fee -->
                      <div class="row">
                        <div class="col-md-12 form-group fee-form-group">
                          <label for="fee" class="col-md-2 control-label align-control-label">Fee</label>
                          <div class="col-md-10">
                            <input class="form-control" placeholder="Fee" v-model="data_form.fee" type="text" >
                            <div class="fee-error"></div>
                          </div>
                        </div>
                      </div>


                      <!-- Status -->
                      <div class="row">
                        <div class="col-md-12 form-group status-form-group">
                          <label for="status" class="col-md-2 control-label align-control-label">Status<span class="required_field">*</span></label>
                          <div class="col-md-10">
                            <div class="col-md-2">Active : <input name="status" id="status_yes" value="1" type="radio" v-model="data_form.status" required=""></div>
                            <div class="col-md-8">Inactive : <input name="status" id="status_no" value="0" type="radio" v-model="data_form.status"></div>
                            <div class="status-error">
                            </div>
                          </div>
                        </div>
                      </div>

                      <!-- Attributes -->
                      <div class="row" style="border-bottom: 1px solid #e5e5e5;">
                        <div class="col-md-2 modal-title " style="padding:15px;" >
                          <h4 class="modal-title">Attributes</h4>
                        </div>
                        <div class="col-md-4 checkbox " style="padding:10px 10px 10px 15px;" >
                          <label><input type="checkbox" id="checkAllCheckbox" v-on:click="selectAll" v-model="checkAll"  /> Check all</label>
                        </div>
                      </div>

                      <div style="height:200px;overflow-y:scroll;overflow-x:hidden;">
                        <div v-for="attribute in attributeList" class="col-md-4 modal-title " style="padding:0px 5px 0px 15px;" >
                          <div class="checkbox">
                            <label>
                              <input name="checkedNames[]" type="checkbox" v-bind:id="attribute.id" class="report-attributes" v-bind:value="attribute.id" > {{ attribute.name }} 
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
                    <button type="button" class="btn btn-default" data-dismiss="modal" >Close</button>
                    <button type="submit" class="btn btn-primary">Submit</button>
                  </div>
                  <!--/ modal-footer -->

                </div>
                <!--/ modal-content -->
              </form>
            </div>
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
          <!-- Modal Dialog -->
        </div>

      </div>
    </div>

    <delete-vue></delete-vue>

  </div>
</template>


<script type="text/javascript">

  import deleteVue from '../common/delete.vue';

  export default {

    mounted() {
      this.$parent.page_title = "Tests";
      
      if($.isEmptyObject(this.$parent.test_reports)){
          this.getRecords(false);
      }        

     /* var vm =this;
      setTimeout(function () {
          vm.applySelect2();
          }, 100);*/
    },

    data: function() {
      return {
        isloading: true,
       

        showloader: false,
        error:{},
        test_reports: this.$parent.test_reports,

        
        records:[],
        data_form:{},
        status_info:{'id':'','status':''},
        status_confirmation:'',
        attributeList:{},
        checkedAttributes:[],
        checkAll:false,

      }
    },

    components: {
      'deleteVue': deleteVue
    },

    methods: {

      getRecords: function(destroy=false){
        this.showloader = true;
        this.$http.get('/tests').then((response) => {
        this.showloader = false;
        this.test_reports = response.body.records;
        this.$parent.test_reports = this.test_reports;
        //this.attributeList=response.body.attributeList;

          // DataTable
         /* this.isloading = false;
          if(destroy){
            $('#record_table').DataTable().destroy();
          }
          setTimeout(function () {
            $('#record_table').DataTable({
              "aLengthMenu": [[5, 10, -1], [5, 10, "All"]],
              "iDisplayLength": 5
            });
          }, 100);*/

      }, (response) => {
          console.log(response);
      });


    },
     
    callGroupAttribute: function(){
      
      var labgroup=this.data_form.test_group;
        this.$http.get('/api/attribute_group/'+labgroup).then((response) => {
          this.attributeList=response.body.attributeList;
          console.log(this.attributeList);    
     

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
      this.showloader = true;
      this.resetForm();
      this.data_form = {};
      if(rec_id!="0"){
        this.$http.get('#/test_report/'+rec_id).then((response) => {
          this.data_form = response.body.record;
          this.checkedAttributes= response.body.checkedAttributes;
          this.data_form.id = rec_id;
          
          this.showloader = false;
          
          this.callGroupAttribute(response.body.record.test_group);
          var checkedAttributes = this.checkedAttributes;
          console.log(checkedAttributes);
         
          $(".report-attributes").each(function(){
              if($.inArray(parseInt($(this).val()),checkedAttributes)!=-1){
                $(this).prop("checked",true);
                $(this).attr("checked","checked");
              }
          });

        
        }, (response) => {
          this.showloader = false;
          console.log(response);
        });
      }else{
        this.showloader = false;
      }

    },
    
    submitForm: function(){

      
      var checkedItems = $("input[name='checkedNames[]']:checked");
      var test=[];
      $.map(checkedItems, function(val, key) { 
        test.push(val.value);
      }); 
     this.data_form.attribute_id=test;



      this.showloader =  true;
      var routes = this.$http.post('/api/test_reports',this.data_form);
      routes.then((response) => {
         
          this.showloader =  false;
          if(response.body.isvalid){
              if(response.body.success){
                  this.error = {'type':'text-success' ,'message':'Updated successfully'};
                  this.getRecords(true);
                  this.data_form = {};

              }else{
                  this.error = {'type':'text-danger' ,'message':response.body.message};
              }
          }else{
              this.errors = response.body.errors;
          }
          $("#add-edit-record").modal("hide");
      }, (response) => {
          console.log(response);
      });
    },

    setRecordToDelete: function(record_id){
        this.record_to_delete = record_id;
    },

    deleteRecord: function(){
        this.showloader = true;
        var record_id = this.record_to_delete;
        this.$http.delete('/api/test_report/'+record_id).then((response) => {
            this.showloader = false;
            console.log(response.body.success);
            if(response.body.success){
                if(!this.showloader){
                    this.error = {'type':'text-success','message':'Deleted successfully.'};
                }
                this.getRecords(true);
            }else{
                this.error = {'type':'text-danger','message':response.body.message};
                console.log(response.body);
            }
        }, (response) => {
            console.log(response);
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

      this.showloader = true;
      var input = this.status_info;
      this.$http.post('/api/attribute_status',input).then((response) => {
        this.showloader = false;
        this.error = {'type':'text-success','message':'Updated successfully.'};
        $("#confirmStatus").modal('hide');
        this.getRecords(true);
        this.status_info = {'id':'','status':''};

      }, (response) => {
        this.error = {'type':'text-danger','message':response.body.message};
        console.log(response.body);

      });

    }
    
    
  }
}
</script>

