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
            <button v-on:click="adddoctor" class="row btn btn-primary btn-sm pull-right">Add new doctor</button>
        </div>
      <table id="doctor_tbl" class="table table-striped b-t b-b list-table">
        <thead>
          <tr>
            <th width="25%" class="table-th">Name</th>
            <th width="25%" class="table-th">Address</th>
            <th width="25%" class="table-th">Contact</th>
            <th width="25%" class="table-th">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(doctor,index) in doctors">
            <td class="table-td">{{doctor.name}}</td>
            <td class="table-td">{{doctor.address}}</td>
            <td class="table-td">{{doctor.contact_no}}</td>
            <td class="table-td">
                <button v-on:click="updatedoctor(doctor.id)" class="btn btn-primary" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-edit"></i> Edit
                </button>
                <button data-toggle="modal" data-target="#confirmDelete" v-on:click="setdoctorToDelete(doctor.id)" class="btn btn-danger" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-trash"></i> Delete
                </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  </div>
</div>
{{ ValidPageAccess }}
<delete-vue></delete-vue>
<unauthorised-vue v-if="!showPage"></unauthorised-vue>
  </div>
</template>
<script>
  import deleteVue from '../common/delete.vue';
  import unauthorisedVue from '../common/unauthorised.vue';
  export default {
    mounted() {
      this.$parent.showAppPage=false;
      this.$parent.page_title = "";

/*        console.log("All Doctors");
        this.$parent.page_title = "All Doctors";
        if($.isEmptyObject(this.$parent.doctors)){
            this.getAllDoctors();
        }else{
          setTimeout(function() {
              $("#doctor_tbl").dataTable();
          }, 100);
        }*/

    },
    data: function() {
        return {
            showloader: false,
            error:{},
            doctors: this.$parent.doctors,
            types:{"1":"Header","2":"Footer","3":"Invoice"},
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
      'unauthorisedVue':unauthorisedVue
    },
    methods: {
        adddoctor: function(){
         location.href = '#/add-doctor';
       },
       getAllDoctors: function(){
            this.showloader = true;
            this.$http.get('/doctors').then((response) => {
                this.showloader = false;
                this.doctors = response.body.doctors;
                this.$parent.doctors = this.doctors;
                setTimeout(function() {
                    $("#doctor_tbl").dataTable();
                }, 100);
            }, (response) => {
                console.log(response);
            });

       },
       updatedoctor: function(doctor_id){
            location.href = '#/doctor/'+doctor_id;
       },
       setdoctorToDelete: function(doctor_id){
            this.doctor_id_to_delete = doctor_id;
       },
       deleteRecord: function(){
            this.showloader = true;
            var doctor_id = this.doctor_id_to_delete;
            this.$http.delete('/doctors/'+doctor_id).then((response) => {
                this.showloader = false;
                console.log(response.body.success);
                if(response.body.success){
                    if(!this.showloader){
                        this.error = {'type':'text-success','message':'Deleted successfully.'};
                    }
                    this.$parent.doctors = {};
                    //this.getAllDoctors();
                }else{
                    this.error = {'type':'text-danger','message':response.body.message};
                    console.log(response.body);
                }
            }, (response) => {
                console.log(response);
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
              this.$parent.page_title = "All Doctors";
              this.$parent.showAppPage=true;  
              this.showPage=true;
              this.getAllDoctors();
          

              

              /*        console.log("All Doctors");
              this.$parent.page_title = "All Doctors";
              if($.isEmptyObject(this.$parent.doctors)){
                  this.getAllDoctors();
              }else{
                setTimeout(function() {
                    $("#doctor_tbl").dataTable();
                }, 100);
              }*/
                                   
          }
       }       



    }
  }
</script>
