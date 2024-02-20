<template>
  <div class="modal fade in" id="searchPatient" role="dialog" aria-labelledby="exampleModalLabel">
    <div class="col-sm-8 col-sm-offset-2 modal-dialog-30">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
          <h4 class="modal-title">Search Patient</h4>
        </div>
        <div class="modal-body">
          <div class="col-sm-12 form-group no-padding" style="float:none;">
            <input v-on:keyup="searchPatient" v-model="search_string" placeholder="Patient Search" class="form-control" type="text">
          </div>
          <div class="table-responsive">
            <div v-show="showloader" class="text-center loader">
                <img src="/images/loading.gif">
            </div>
            <table id="doctor_tbl" class="table table-striped b-t b-b list-table">
              <thead>
                <tr>
                  <th width="25%" class="table-th">Name</th>
                  <th width="25%" class="table-th">Mobile</th>
                  <th width="25%" class="table-th">Email</th>
                  <th width="25%" class="table-th text-right">Action</th>
                </tr>
              </thead>
              <tbody>
                <tr><th colspan="4" class="text-center">Patients From Lab</th></tr>
                <tr v-for="(patient,index) in patients">
                  <td class="table-td">{{patient.first_name}} {{patient.last_name}}</td>
                  <td class="table-td">{{patient.mobile}}</td>
                  <td class="table-td">{{patient.email}}</td>
                  <td class="table-td text-right">
                      <button v-on:click="selectPatient(patient.id)" type="button" class="btn btn-primary" style="padding: 0 10px;" data-dismiss="modal" aria-hidden="true">select</button>
                  </td>
                </tr>
                <tr v-if="!patients.length"><td colspan="4">No Patients Found From Lab</td></tr>
                <tr><th colspan="4" class="text-center">Patients From Pathonet</th></tr>
                <tr v-for="(patient,index) in all_patients">
                  <td class="table-td">{{patient.first_name}} {{patient.last_name}}</td>
                  <td class="table-td">{{patient.mobile}}</td>
                  <td class="table-td">{{patient.email}}</td>
                  <td class="table-td text-right">
                      <!-- <button v-if="patient.mobile_matched" v-on:click="selectPatientFromMain(patient.id)" type="button" class="btn btn-primary" style="padding: 0 10px;" data-dismiss="modal" aria-hidden="true">select</button> -->
                      <button v-if="patient.mobile_matched" type="button" v-on:click="getAuthBy(patient.dobExist,patient.id)" data-toggle="modal" data-target="#checkInfo"  class="btn btn-primary" style="padding: 0 10px;">
                        Authorize
                      </button>
                  </td>
                </tr>
                <tr v-if="!all_patients.length"><td colspan="4">No Patients Found From Other Lab</td></tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
    <!-- Modal Dialog -->
      <div class="modal fade" id="checkInfo" role="dialog" aria-labelledby="confirmStatusLabel" aria-hidden="true">
        <div class="col-sm-6 col-sm-offset-3 modal-dialog-30">
          <div class="modal-content">
            <div class="modal-header">
              <button type="button" class="close" v-on:click="closeCheckInfo" aria-hidden="true">&times;</button>
              <h4 class="modal-title">Authorize to get data</h4>
            </div>
            <div class="modal-body">
              <div v-show="error" class="col-sm-12">
                  <div v-bind:class="error.type" class="text-center">
                      {{error.message}}
                  </div>
              </div>
              <input v-on:change="checkPatientDOB(patient_id_main_db,dob)" v-show="dobExist" type="text" v-model="dob" id="user_dob" value="" placeholder="Date of Birth">
              <input v-show="!dobExist" type="text" v-model="otp"  placeholder="One Time Password">
              <button v-on:click="sendSMSToPatient(patient_id_main_db)" v-show="!dobExist && !verifying" type="button" class="btn btn-primary" id="sms_btn">Send Data</button>
              <button v-on:click="verifyOTP(patient_id_main_db)" v-show="!dobExist && verifying" type="button" class="btn btn-primary">Verify OTP</button>
            </div>
            <div class="modal-footer">
              <button v-on:click="selectPatientFromMain(patient_id_main_db)" v-if="verified" type="button" class="btn btn-primary">Get Data</button>
              <button type="button" class="btn btn-default" v-on:click="closeCheckInfo">Cancel</button>
            </div>
          </div>
        </div>
      </div>
      <!-- Modal Dialog -->
  </div>
</template>
<script>
  export default {
    mounted() {
        this.searchPatient();
    },
    data: function() {
        return {
            showloader: false,
            patients : [],
            search_string : "",
            all_patients : [],
            dob:"",
            otp:"",
            dobExist:true,
            verified:false,
            error:{},
            patient_id_main_db:"",
            verifying:false
        }
    },
    methods: {
      resetAuthForm:function(){
        this.dob = "";
        this.otp = "";
        this.dobExist = true;
        this.verified = false;
        this.error = {};
        this.patient_id_main_db = "";
        this.verifying = false;
      },
      searchPatient:_.debounce(function (e) {
          this.showloader =  true;
          this.$http.post('/search_patient',{search_string : this.search_string}).then((response) => {
              this.showloader =  false;
              console.log(response.body);
              this.patients = response.body.lab_patients;
              this.all_patients = response.body.all_patients;
          }, (response) => {
              console.log(response);
          });
        }, 500),
      selectPatient: function(patient_id){
        this.$parent.showloader = true;
        this.$http.get('/patient/'+patient_id).then((response) => {
            this.$parent.patient = response.body.patient;
            var vm = this;
            setTimeout(function(){
                vm.$parent.applySelect2();
            },10);
            this.$parent.showloader = false;
        }, (response) => {
            console.log(response);
        });
      },
      selectPatientFromMain: function(patient_id){
        this.$parent.showloader = true;
        $("#checkInfo,#searchPatient").modal("hide");
        this.$http.get('/get_patient_from_main/'+patient_id).then((response) => {
            this.$parent.patient = response.body.patient;
            var vm = this;
            setTimeout(function(){
                vm.$parent.applySelect2();
            },10);
            this.$parent.showloader = false;
        }, (response) => {
            console.log(response);
        });
      },
      closeCheckInfo: function(){
        $("#checkInfo").modal("hide");
      },
      sendSMSToPatient: function(patient_id){
        this.$http.get('/send_patient_otp/'+patient_id).then((response) => {
            this.verifying = true;
            console.log(response.body.success);
            this.error = {type:"text-success",message:"SMS Send to patient number:"+patient_id};
        }, (response) => {
            console.log(response);
        });
      },
      verifyOTP: function(patient_id){
        this.$http.post('/verify_patient_otp/'+patient_id,{otp:this.otp}).then((response) => {
            console.log(response.body.success);
            if(response.body.success){
              this.error = {type:"text-success",message:"OTP Matched"};;
              this.verified = true;
            }else{
              this.error = {type:"text-danger","message":response.body.message};
            }
        }, (response) => {
            console.log(response);
        });
      },
      checkPatientDOB: function(patient_id,dob){
        this.$http.post('/check_patient_dob/'+patient_id,{dob:dob}).then((response) => {
            if(response.body.verified){
              this.error = {type:"text-success","message":"Date matched."};
              this.verified = true;
            }else{
              this.verified = false;
              this.error = {type:"text-warning","message":"Date not matched."};
            }
            console.log(response.body.verified);
        }, (response) => {
            console.log(response);
        });
      },
      getAuthBy: function(dobExist,patient_id){
        this.resetAuthForm();
        this.patient_id_main_db = patient_id;
        this.verified = false;
        this.error = "";
        if(dobExist){
          this.dobExist = true;
          var vm = this;
          $("#user_dob").datepicker({
              dateFormat: 'd M yy',maxDate:'0',changeYear:true,yearRange: "-100:+0",
              onSelect: function(){
                  vm.error = "";
                  vm.dob = $(this).val();
                  vm.checkPatientDOB(patient_id,$(this).val());
                  console.log(vm.dob);
               }
          });
        }else{
          this.dobExist = false;
        }
      }
    }

  }
</script>
<style type="text/css">
  .ui-datepicker-year {
      color: #999 !important;
  }
</style>