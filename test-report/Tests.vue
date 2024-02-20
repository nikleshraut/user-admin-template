<template>
 <div>


<div class="padding" >
    <div class="box-header">
      <!-- / .modal -->
<div >
    <div v-show="showloader" class="text-center loader">
        <img src="/images/loading.gif">
    </div>

    <div class="table-responsive"  v-if="showPage">
        <div v-show="error" class="col-sm-12">
            <div v-bind:class="error.type" class="text-center">
                {{error.message}}
            </div>
        </div>
        <div class="col-sm-12 form-group">
            <button v-on:click="addRecord" class="row btn btn-primary btn-sm pull-right">Add new test</button>
        </div>
      <table id="data_tbl" class="table table-striped b-t b-b list-table">
        <thead>
          <tr>
            <th width="20%" class="table-th">Test</th>
            <th width="20%" class="table-th">Group</th>
            <th width="20%" class="table-th">Unit</th>
            <th width="20%" class="table-th">Status</th>
            <th width="20%" class="table-th">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(record,index) in records">
            <td class="table-td">{{record.name}}</td>
            <td class="table-td">{{record.test_group}}</td>
            <td class="table-td">{{record.unitname}}</td>
            <td class="table-td">
              <a href="#" v-if="record.status==0" data-toggle="modal" data-target="#confirmStatus"@click.prevent="updateStatus(record.id,0)"><span class="text-danger">Inactive</span></a>
              <a href="#" v-if="record.status==1" data-toggle="modal" data-target="#confirmStatus" @click.prevent="updateStatus(record.id,1)"><span class="text-success">Active</span></a>
            </td>             
            <td class="table-td">
                <button v-on:click="updateRecord(record.id)" class="btn btn-primary" style="padding: 0 10px;">
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
  </div>
  {{ ValidPageAccess }}
  </div>
</div>

<delete-vue></delete-vue>
<status-vue></status-vue>
<unauthorised-vue v-if="!showPage"></unauthorised-vue>
  </div>
</template>
<script>
  import deleteVue from '../common/delete.vue';
  import statusVue from '../common/status.vue';
  import unauthorisedVue from '../common/unauthorised.vue';

  export default {
    mounted() {

      this.$parent.showAppPage=false;
      this.$parent.page_title = "";

        /*this.$parent.page_title = "All Tests";
        this.getRecords();*/
         
        
    },
    data: function() {
        return {
          showloader: false,
          error:{},
          tests: this.$parent.tests,
          status_info:{'id':'','status':''},
          records:{},
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
      'deleteVue': deleteVue,
      'statusVue':statusVue,
      'unauthorisedVue':unauthorisedVue
    },
    methods: {
        addRecord: function(){
         location.href = '#/add-test';
       },
       getRecords: function(){
            this.showloader = true;
            this.$http.get('/tests').then((response) => {
                this.showloader = false;
                this.records = response.body.records;
                this.$parent.tests = this.records;
                setTimeout(function() {
                    $("#data_tbl").dataTable();
                }, 100);
            }, (response) => {
                console.log(response);
            });

       },
       updateRecord: function(record_id){
            location.href = '#/test/'+record_id;
       },
       setRecordToDelete: function(record_id){
            this.record_id_to_delete = record_id;
       },
       deleteRecord: function(){
            this.showloader = true;
            var record_id = this.record_id_to_delete;
            this.$http.delete('/tests/'+record_id).then((response) => {
                this.showloader = false;
                console.log(response.body.success);
                if(response.body.success){
                    if(!this.showloader){
                        this.error = {'type':'text-success','message':'Deleted successfully.'};
                    }
                    this.$parent.doctors = {};
                    //this.getRecords();
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
          var nStatus=(vStatus==0)?'active':'inactive';
          this.status_confirmation= 'Are you sure to change status as '+nStatus+' ?';
        },       

        changeStatus: function(){

          this.showloader = true;
          var input = this.status_info;
          this.$http.post('/api/test_status',input).then((response) => {
            this.showloader = false;
            this.error = {'type':'text-success','message':'Updated successfully.'};
            $("#confirmStatus").modal('hide');
            this.getRecords(true);
            this.status_info = {'id':'','status':''};

          }, (response) => {
            this.error = {'type':'text-danger','message':response.body.message};
            console.log(response.body);

          });

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
              this.$parent.page_title = "All Tests";
              this.$parent.showAppPage=true;  
              this.showPage=true;
              this.getRecords(); 

                               
            }
       }  



    }
  }
</script>
