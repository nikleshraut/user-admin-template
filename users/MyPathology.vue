<template>
    <div class="full-height page-form" style="background-color: #fff;">
    <form  v-if="showPage"  id="test_registration_page" class="form-body full-height" style="overflow: auto;background-color: #fff;" role="form" method="POST" v-on:submit.prevent="submitForm">
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
                        List of Pathology
                    </div>
                </div>
                <div style="position:absolute;right:0px;bottom:0px;z-index: 99;">
                    <span v-on:click="addLab" style="
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
                    <div class="col-sm-12">
                        <div class="col-sm-2">
                            <span class="btn no-padding" style="
                            background-color: #ccc;
                            width: 64px;
                            height: 64px;
                            line-height: 64px;
                            font-size: 32px;
                            color: #000;
                            "><i class="fa fa-hospital-o"></i>
                            </span>
                        </div>
                        <div class="col-sm-4">
                            <div><strong class="text-bold">{{lab.lab_name}}</strong></div>
                            <div>{{lab.address}}, {{lab.address_area}}, {{lab.city}}</div>
                            <div>{{lab.district}}, {{lab.pincode}}, {{lab.state}}</div>
                            <div>
                                <span v-if="lab.lab_phone!=''">{{lab.lab_phone}}</span>
                                <span v-if="lab.lab_phone!='' && lab.owner_contact_no!=''">,</span>
                                <span v-if="lab.owner_contact_no!=''">{{lab.owner_contact_no}}</span>
                            </div>

                            <div>
                                <span v-if="lab.lab_host_name!=''">URL: <b>http://{{lab.lab_host_name}}.{{$parent.app_domains.lab_admin_domain}}.{{$parent.app_domains.server_domain_url}}</b></span>
                            </div>
                            <div>Status :
                            <span v-bind:class="lab.lab_status==1?'text-primary':'text-danger'">
                                <span v-if="lab.lab_status==0">Inactive</span>
                                <span v-if="lab.lab_status==1">Active</span>
                                <span v-if="lab.lab_status==2">Pending for approval</span>
                                <span class="pointer" v-if="lab.lab_status==3" v-on:click="addLicence(lab.lab_id)">Setup Incomplete</span>
                                <span v-if="lab.lab_status==4">Expired</span>
                            </span></div>
                        </div>
                        <div class="col-sm-3">
                            <div class="form-group">
                                <button type="button" class="btn btn-default" v-on:click="viewLab(lab.lab_id)"><div class="btn-text">Detail</div></button>
                            </div>
                        </div>
                        <div class="col-sm-3">
                            <div class="form-group" v-if="lab.lab_status==1">
                                <button v-on:click="visitLab(lab.lab_host_name)" style="0 10px 12px -10px #125889" type="button" class="btn btn-primary"><div class="btn-text">Go to Lab</div></button>
                            </div>
                            <div class="form-group">
                                <button style="0 10px 12px -10px #125889" type="button" class="btn btn-primary"  v-on:click="editLab(lab.lab_id)"><div class="btn-text">Edit Lab</div></button>
                            </div>


                        </div>
                    </div>
                </div>

                <div class="col-sm-12">
                    <div class="col-sm-12">
                        <hr style="">
                    </div>

                </div>
                <!-- {{ profile }} -->  <!-- {{ computed_check_access }} -->
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

        console.log("My Pathology");
        this.$parent.page_title = "My Pathology";
        this.$parent.back_url = "#/";
        this.$parent.help_content = "<h4>My Pathology</h4> help content";
        if($.isEmptyObject(this.$parent.labs)){
            this.getAllLabs();
        }

    },
    data: function() {
        return {
            showloader: false,
            error:{},
            labs: this.$parent.labs,
            types:{"1":"Header","2":"Footer","3":"Invoice"},
            profile:this.$parent.profile,
            showPage:false,
            valid_access:false,

        }
    },
    computed:{
        computed_check_access:function(){
           this.profile=this.$parent.profile;
        },

        ValidPageAccess: function () {

        if(this.$parent.valid_access==true){
            // To restrict access this page at labname.pa.pathonet.dev
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
        addLab: function(){
         location.href = '#/add-lab';
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
       visitLab: function(lab_host){
            window.open("http://"+lab_host+".pa."+this.$parent.host_url, '_blank');
       },

        viewLab: function(lab_id){
         location.href = '#/view-lab/'+lab_id;
       },

        editLab: function(lab_id){
         location.href = '#/edit-lab/'+lab_id;
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
        addLicence: function(lab_id){
            this.$parent.lab_id = lab_id;
            location.href = '#/add-licence';
        },

      checkAccess: function(){

        if(this.valid_access==''){
          this.$parent.validAccess();
        }

       if(this.valid_access==true ){
          this.showloader =  false;
          this.$parent.page_title = "My Pathology";
          this.$parent.back_url = "#/";
          this.$parent.help_content = "<h4>My Pathology</h4> help content";

          this.$parent.showAppPage=true;
          this.showPage=true;
          this.getAllLabs();



        /*console.log("My Pathology");
        this.$parent.page_title = "My Pathology";
        this.$parent.back_url = "#/";
        this.$parent.help_content = "<h4>My Pathology</h4> help content";
        //if($.isEmptyObject(this.$parent.labs)){
            this.getAllLabs();
        //}
        */
        }
      }

    }
  }
</script>
