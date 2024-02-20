<template>
  <div>
    <div class="padding" > 
      <div class="box-header">
        <!-- / .modal -->
        <div >
          <div v-show="showloader" class="text-center loader">
            <img src="/images/loading.gif">
          </div>

          <div class="table-responsive" v-if="showPage">
            <div v-show="error" class="col-sm-12">
              <div v-bind:class="error.type" class="text-center">
                {{error.message}}
              </div>
            </div>
            <div class="col-sm-12 form-group">
              <button v-on:click="addRecord" class="row btn btn-primary btn-sm pull-right">Add new unit</button>
            </div>
            <table id="record_table" class="table table-striped b-t b-b list-table">
              <thead>
                <tr>
                  <th width="30%" class="table-th">Description</th>
                  <th width="35%" class="table-th">Unit</th>
                  <th width="15%" class="table-th">Status</th>
                  <th width="20%" class="table-th">Action</th>
                </tr> 
              </thead>
              <tbody>
                <tr v-for="(unit,index) in units">
                  <td class="table-td">{{unit.description}}</td>
                  <td class="table-td">{{unit.name}}</td>
                  <td class="table-td">
                    <a href="#" v-if="unit.status==0" data-toggle="modal" data-target="#confirmStatus" @click.prevent="updateStatus(unit.id,0)"><span class="text-danger">Inactive</span></a>
                    <a href="#" v-if="unit.status==1" data-toggle="modal" data-target="#confirmStatus" @click.prevent="updateStatus(unit.id,1)"><span class="text-success">Active</span></a>
                  </td>
                  <td class="table-td">
                    <button v-on:click="updateRecord(unit.id)" class="btn btn-primary" style="padding: 0 10px;">
                      <i class="glyphicon glyphicon-edit"></i> Edit
                    </button>
                    <button data-toggle="modal" data-target="#confirmDelete" v-on:click="setRecordToDelete(unit.id)" class="btn btn-danger" style="padding: 0 10px;">
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
      //console.log(this.$options.name);
      this.$parent.showAppPage=false;
      this.$parent.page_title = "";
      //this.showPage=false;
    },
    data: function() {
      return {
        showloader: false,
        error:{},
        units: this.$parent.units,
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
      'deleteVue': deleteVue,
      'statusVue':statusVue,
      'unauthorisedVue':unauthorisedVue
    },
    methods: {
      addRecord: function(){
        location.href = '#/add-unit';
      },
      getRecords: function(){
        this.showloader = true;
        this.$http.get('/units').then((response) => {
            
            this.units = response.body.records;
            this.$parent.units = this.units;
            this.applyDatatable();
            
            this.showloader = false;
        }, (response) => {
          console.log(response);
        });

      },
      updateRecord: function(record_id){
        location.href = '#/unit/'+record_id;
      },
      setRecordToDelete: function(record_id){
        this.record_id_to_delete = record_id;
      },
      deleteRecord: function(){
        this.showloader = true;
        var record_id = this.record_id_to_delete;
        this.$http.delete('/units/'+record_id).then((response) => {
          this.showloader = false;
          console.log(response.body.success);
          if(response.body.success){
            if(!this.showloader){
              this.error = {'type':'text-success','message':'Deleted successfully.'};
            }
            this.$parent.units = {};
            this.getRecords();
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
        this.$http.post('/api/unit_status',input).then((response) => {
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
      applyDatatable: function(){
        setTimeout(function() {
          $("#record_table").dataTable();
        }, 100);
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
            this.$parent.page_title = "Units";
            this.$parent.showAppPage=true;  
            this.showPage=true;
            this.getRecords(); 
                        
          }
       }  

    }
  }
</script>
