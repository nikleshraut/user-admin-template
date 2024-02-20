<template>
<div>
    <form v-if="showPage" id="test_registration_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
        <div class="max-form-width div-center form-center full-width">
            <div class="col-md-10 inner-body full-width no-padding">
                <div v-show="showloader" class="text-center loader">
                    <img src="/images/loading.gif">
                </div>

                <div v-show="error" class="col-sm-12 page-error">
                    <div v-bind:class="error.type" class="text-center">
                        {{error.message}}
                    </div>
                </div>
                <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title">DOCTOR DETAILS</div></div>
                <!-- new row -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Name</label>
                        <input v-model="formData.name" placeholder="Doctor Name" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.name }">{{errors.name?errors.name[0]:''}}
                        </div>
                    </div>
                    <div class="col-md-6 form-group lab_name-form-group">
                        <label class="control-label hidden-xs full-width text-left">Qualification</label>
                        <div class="pos-relative">
                            <select v-model="formData.qualification" id="qualification" name="qualification" class="form-control">
                                <option value="">Select Qualification</option>
                                <option v-for="qualification in qualifications" v-bind:value="qualification.data_key_value">{{qualification.data_key_value}}</option>
                            </select>
                            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.qualification }">{{errors.qualification?errors.qualification[0]:''}}
                            </div>
                        </div>
                    </div>
                </div>
                <!-- row end -->
                <!-- new row -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Clinic/Hospital Name</label>
                        <input v-model="formData.clnc_hsptl_name" placeholder="Clinic/Hospital Name" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.clnc_hsptl_name }">{{errors.clnc_hsptl_name?errors.clnc_hsptl_name[0]:''}}
                        </div>
                    </div>
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Reg. No</label>
                        <input v-model="formData.reg_no" placeholder="Registration No." class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.reg_no }">{{errors.reg_no?errors.reg_no[0]:''}}
                        </div>
                    </div>
                </div>
                <!-- row end -->
                <!-- new row -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Address</label>
                        <input v-model="formData.address" placeholder="Address" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.address }">{{errors.address?errors.address[0]:''}}
                        </div>
                    </div>
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">City</label>
                        <input v-model="formData.city" placeholder="City" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.city }">{{errors.city?errors.city[0]:''}}
                        </div>
                    </div>
                </div>
                <!-- row end -->
                <!-- new row -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Mobile</label>
                        <input v-model="formData.contact_no" placeholder="Mobile" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.contact_no }">{{errors.contact_no?errors.contact_no[0]:''}}
                        </div>
                    </div>
                </div>
                <!-- row end -->

                <div class="col-sm-12 form-group text-center footer-buttons">
                    <div class="form-group">
                    <button v-on:click="AllDoctor" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
                    <div class="form-group">
                    <button type="submit" class="btn btn-primary rounded-corner">Save</button></div>
                </div>

            </div>{{getQualifications}} {{checkFormChanged}}
        </div>
    </form>
  {{ ValidPageAccess }}
  <unauthorised-vue v-if="!showPage"></unauthorised-vue>

</div>
</template>
<script>
import unauthorisedVue from '../common/unauthorised.vue';

  export default {
    mounted() {
      this.$parent.showAppPage=true;//false before
      //this.$parent.page_title = "";

        console.log("New Doctor");
        this.$parent.page_title = "Add/Edit Doctor Info";
        if(this.$route.params.id != undefined){
            this.updateDoctor(this.$route.params.id);
        }else{
            this.applySelect2();
        }
    },
    data: function() {
        return {
            showloader: false,
            errors: {},
            error:{},
            formData:{qualification:""},
            qualifications: this.$parent.qualifications,
            showPage:true,//false before
            valid_access:false
        }
    },
    computed:{
        getQualifications: function(){
            this.qualifications = this.$parent.qualifications;
        },
      ValidPageAccess: function () {
        this.valid_access=this.$parent.valid_access;
        this.checkAccess();
      },
      checkFormChanged:function(){
        if(this.$parent.doctorFormChanged != undefined){
            this.resetForm();
        }
      }
    },
    components: {
      'unauthorisedVue':unauthorisedVue
    },
    methods: {
        resetForm:function(){
            this.error = {};
            this.errors = {};
            this.formData= {};
            $("#qualification").val("").change();
        },
        submitForm: function(){
            this.errors = {};
            this.showloader = true;
            this.$http.post('/doctors',this.formData).then((response) => {
                if(response.body.isvalid){
                    if(response.body.success){
                        this.error = {'type':'text-success' ,'message':'Updated successfully'};
                        this.$parent.doctors = {};
                        this.showloader =  false;
                        this.getAllDoctors();
                    }else{
                        this.showloader =  false;
                        this.error = {'type':'text-danger' ,'message':response.body.message};
                    }
                }else{
                    this.showloader =  false;
                    this.errors = response.body.errors;
                }
            }, (response) => {
                console.log(response);
            });
        },
        applySelect2: function(){
            var vm = this;
            $("#qualification")
            .select2({ data: this.options })
            .on('change', function () {
                console.log($(this).val() + " selected");
                vm.formData.qualification = $(this).val();
            });
            $(".select2-container").addClass("form-select2 text-left").css('width','100%').css('max-width','400px');
            $(".select2-selection").css("border","none");
            $(".select2-selection__rendered").css("padding","0px");
        },
        updateDoctor: function(doctor_id) {
            this.showloader =  true;
            this.$http.get('/doctors/'+doctor_id).then((response) => {
                this.showloader =  false;
                this.formData = response.body.doctor;
                var vm = this;
                setTimeout(function() {
                    vm.applySelect2();
                }, 1);
            }, (response) => {
                console.log(response);
            });
        },
        AllDoctor: function() {
            location.href = '#/doctors';
        },
        getAllDoctors: function() {
            this.$http.get('/doctors').then((response) => {
                this.showloader =  false;
                this.formData = {};
                console.log(response.body.doctors);
                this.$parent.doctor_id_list = response.body.doctors;
                $("#doctor_id").select2("destroy");
                setTimeout(function(){
                    $("#doctor_id").select2();
                    $(".select2-container").addClass("form-select2 text-left").css('width','100%');
                    $(".select2-selection").css("border","none");
                    $(".select2-selection__rendered").css("padding","0px");
                },100);
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
          this.$parent.page_title = "Add/Edit Doctor Info";
          this.$parent.showAppPage=true;
          this.showPage=true;

        if(this.$route.params.id != undefined){
            this.updateDoctor(this.$route.params.id);
        }else{
            this.applySelect2();
        }

        /*console.log("New Doctor");
        this.$parent.page_title = "Add/Edit Doctor Info";
        if(this.$route.params.id != undefined){
            this.updateDoctor(this.$route.params.id);
        }else{
            this.applySelect2();
        }
        */

        }
      }

    }
  }
</script>