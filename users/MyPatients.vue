<template>
<div>
    <form  v-if="showPage"   id="test_registration_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
        <div class="max-form-width div-center form-center full-width">
            <div class="col-md-10 inner-body full-width no-padding">
                <div v-show="showloader" class="text-center loader">
                    <!-- <img src="/images/loading.gif"> -->
                </div>

                <div v-show="error" class="col-sm-12 page-error">
                    <div v-bind:class="error.type" class="text-center">
                        {{error.message}}
                    </div>
                </div>
                <div class="col-sm-12">
                    <div class="col-sm-12 form-group page-sub-title">
                        List of Patients
                    </div>
                </div>
                <div style="position:absolute;right:0px;bottom:0px;z-index: 99;">
                    <span style="
                            width: 56px;
                            height: 56px;
                            line-height: 56px;
                            font-size: 20px;
                            color: #fff;
                            " class="btn no-padding btn-primary"><i class="fa fa-plus"></i></span>
                </div>
                <div class="col-sm-12" v-for="lab in labs">
                    <div class="col-sm-12">
                        <hr style="">
                    </div>
                    <div class="col-sm-12" style="height:90px;">
                        <div class="col-sm-2" style="height: 100%;align-items: center;display: flex;">
                            <span class="btn no-padding" style="
                            background-color: #ccc;
                            width: 64px;
                            height: 64px;
                            line-height: 64px;
                            font-size: 32px;
                            color: #000;
                            "><i class="fa fa-wheelchair-alt"></i>
                            </span>
                        </div>
                        <div class="col-sm-4">
                            <div><strong class="text-bold">Patient 1</strong></div>
                            <div>Sadar, Nagpur</div>
                            <div>Nagpur, 440011</div>
                            <div>1234567890</div>
                        </div>
                        <div class="col-sm-3 text-right" style="height: 100%;display: flex;align-items: flex-end;">
                            <div><button type="button" class="btn btn-default"><div class="btn-text">Detail</div></button></div>
                        </div>
                        <div class="col-sm-3" style="height: 100%;display: flex;align-items: flex-end;">
                            <div>
                                <div class="text-center">
                                    <button style="0 10px 12px -10px #125889" type="button" class="btn btn-primary form-group"><div class="btn-text">Edit Patient</div></button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col-sm-12">
                    <div class="col-sm-12">
                        <hr style="">
                    </div>
                </div>
            </div>
        </div>
    </form>
    {{ ValidPageAccess }}
    <unauthorised-vue v-if="!showPage"></unauthorised-vue>

    </div>
</template>
<script>
  import deleteVue from '../common/delete.vue';
  import unauthorisedVue from '../common/unauthorised.vue';

  export default {
    mounted() {
      //this.$parent.showAppPage=false;
      //this.$parent.page_title = "";

        console.log("My Patients");
        this.$parent.page_title = "My Patients";
        this.$parent.back_url = "#/";
        this.$parent.help_content = "<h4>My Patients</h4> help content";
        /*if($.isEmptyObject(this.$parent.labs)){
            this.getAllLabs();
        }*/
    },
    data: function() {
        return {
            showloader: false,
            error:{},
            labs: this.$parent.labs,
            types:{"1":"Header","2":"Footer","3":"Invoice"},
            showPage:false,
            valid_access:false,

        }
    },
   computed: {
      ValidPageAccess: function () {
        if(this.$parent.valid_access==true){
            if(this.$parent.app_host.sub_domain==this.$parent.app_domains.lab_admin_domain){
                this.$parent.valid_access=false;
            }
        }
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
       getAllLabs: function(){
            this.showloader = true;
            this.$http.get('/api/pathologies').then((response) => {
                this.showloader = false;
                this.labs = response.body.records;
                this.$parent.labs = this.labs;
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
        this.showloader =  false;
        this.$parent.page_title = "My Patients";
        this.$parent.back_url = "#/";
        this.$parent.help_content = "<h4>My Patients</h4> help content";
        this.$parent.showAppPage=true;
        this.showPage=true;
        if($.isEmptyObject(this.$parent.labs)){
            this.getAllLabs();
        }

        /* console.log("My Patients");
        this.$parent.page_title = "My Patients";
        this.$parent.back_url = "#/";
        this.$parent.help_content = "<h4>My Patients</h4> help content";
        if($.isEmptyObject(this.$parent.labs)){
            this.getAllLabs();
        }*/

        }
      }


    }
  }
</script>
