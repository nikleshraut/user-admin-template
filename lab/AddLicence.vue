<template>
    <div style="padding: 10px 0;">
    <div v-if="showPage" class="col-sm-10 col-sm-offset-1">
        <div v-show="showloader" class="text-center loader">
            <img src="/images/loading.gif">
        </div>
        <div class="progress col-sm-12 no-padding">
          <div class="lab-progress-bar" role="progressbar" style="width:50%">
            1. Lab Info
          </div>
          <div class="lab-progress-bar lab-progress-bar-active" role="progressbar" style="width:50%">
            2. Licence
          </div>
        </div>
        <form class="form-body" role="form" method="POST" v-on:submit.prevent="submitForm">
            <!-- <div class="row form-group" v-show="this.previous">
              <div class="col-sm-3">
                <button type="button" v-on:click="addLab" class="btn btn-default col-sm-12">&lt;&lt; Previous</button>
              </div>
            </div> -->
            <div class="col-md-10 col-md-offset-1">
                <div class="col-md-12 form-group">
                    <input name="is_licence" type="radio" value="false" v-on:change="toggleLicenceInput">
                    <label for="two">Request 15 days trial</label>
                </div>
            </div>
            <div class="col-md-10 col-md-offset-1">
                <div class="col-md-12 form-group">
                    <input name="is_licence" type="radio" checked="" value="true" v-on:change="toggleLicenceInput">
                    <label for="one">Add licence</label>
                </div>
            </div>
            <div class="col-md-10 col-md-offset-1" v-if="is_licence" style="background-color: #f2f2f4;padding: 40px 0;">
                <div class="col-md-10 col-md-offset-1 form-group licence-form-group">
                    <input class="md-input" placeholder="Serial No." v-model="formData.serial_no" type="licence" required="" style="background-color: #fff;padding-left: 15px;">
                    <div v-bind:class="{ 'text-danger': errors.serial_no }">{{errors.serial_no?errors.serial_no[0]:'&nbsp;'}}
                    </div>
                </div>
                <div class="col-md-10 col-md-offset-1 form-group licence-form-group">
                    <input class="md-input" placeholder="Licence Key" v-model="formData.licence_key" type="licence" required="" style="background-color: #fff;padding-left: 15px;">
                    <div v-bind:class="{ 'text-danger': errors.licence_key }">{{errors.licence_key?errors.licence_key[0]:'&nbsp;'}}
                    </div>
                </div>
                <div v-show="error" class="col-md-10 col-md-offset-1 form-group licence-form-group">
                    <div v-bind:class="{ 'text-danger': error }">{{error?error:'&nbsp;'}}
                    </div>
                </div>

                <div class="col-md-10 col-md-offset-1 text-center">
                    <button v-on:click="getLicenceValue" type="button" class="btn btn-licence-apply">Apply</button>
                </div>
            </div>
            <div v-show="this.message != ''" class="col-md-10 col-md-offset-1" style="border: 1px solid #c4c1c0;margin-top: 15px;color: #9B9B9B;">
                <div class="col-md-12 form-group">
                    <p>
                        {{message}}
                    </p>
                </div>
            </div>
            <div class="row">
                <div class="col-md-10 col-md-offset-1 form-group text-center" style="margin-top: 30px;">
                    <button type="submit" class="btn btn-primary form-control1">Submit</button>
                </div>
            </div>

        </form>
        </div>
<!-- {{ ValidPageAccess }}
<unauthorised-vue v-if="!showPage"></unauthorised-vue> -->

    </div>
</template>

<script>
    import unauthorisedVue from '../common/unauthorised.vue';

    export default {
        mounted() {
            //this.$parent.showAppPage=false;
            //this.$parent.page_title = "";

            console.log('Add Licence');
            this.$parent.page_title = "Licence";
            if(!this.lab_id){
                location.href = '#/add-lab';
            }else{
                if(this.$parent.lab_detail == undefined){
                    this.updatelab(this.lab_id);
                }else{
                    //this.previous = true;
                }
            }
        },
        data: function(){
            return{
                lab_id:this.$parent.lab_id,
                is_licence: true,
                //previous: false,
                formData:{serial_no:"",licence_key:""},
                errors:[{}],
                error:"",
                licence_error:"",
                showloader: false,
                showPage:true,
                valid_access:false,
                message:"",
            }
        },
        computed:{


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
          'unauthorisedVue':unauthorisedVue
        },
        methods:{
            submitForm: function(){
                this.showloader =  true;
                this.licence_error = "";
                var formData = {lab_id:this.lab_id};
                if(this.is_licence){
                    formData.is_licence = true;
                    formData.serial_no = this.formData.serial_no;
                    formData.licence_key = this.formData.licence_key;
                }else{
                    formData.is_licence = false;
                }
                console.log(formData);
                this.$http.post('/api/add_licence',formData).then((response) => {
                    this.showloader =  false;
                    console.log(response.body);
                    if(response.body.isvalid){
                        if (response.body.success) {
                            if(this.is_licence){
                                this.changeLabStatus(this.lab_id,1);
                            }
                            location.href = '#/my-pathology';
                        }else{
                            console.log("error in update");
                            this.error = response.body.message;
                        }
                    }else{
                        this.errors = response.body.errors;
                    }
                }, (response) => {

                });
            },
            updatelab: function(lab_id){
                this.$http.get('/api/pathology/'+lab_id).then((response) => {
                    this.$parent.lab_detail = response.body.lab_detail;
                    this.$parent.lab_detail.id = lab_id;
                    //this.previous = true;
                }, (response) => {
                    console.log(response);
                });
            },
            addLabUser: function(){
                   location.href = '#/add-lab-user';
            },
            addLab: function(){
                location.href = '#/add-lab';
            },
            toggleLicenceInput: function(){
                this.is_licence = !this.is_licence;
                if(!this.is_licence){
                    this.message = 'You are requesting to get 15 days trial period after apporval. After 15 days you need to buy licence or you will be no longer to use our services.';
                }else{
                    this.message = '';
                }
            },
            changeLabStatus: function(lab_id,status){
                var input = {id:lab_id,status:status};
                this.$http.post('/api/lab_status',input).then((response) => {
                    //console.log(response.body);
                }, (response) => {

                });
            },

            checkAccess: function(){

                if(this.valid_access==''){
                    this.$parent.validAccess();
                }

                if(this.valid_access==true ){
                    this.showloader =  false;
                    this.$parent.page_title = "Licence";
                    //this.$parent.back_url = "#/";
                    //this.$parent.help_content = "<h4>My Pathology</h4> help content";

                    this.$parent.showAppPage=true;
                    this.showPage=true;

                    if(!this.lab_id){
                        location.href = '#/add-lab';
                    }else{
                        if(this.$parent.lab_detail == undefined){
                            this.updatelab(this.lab_id);
                        }else{
                            //this.previous = true;
                        }
                    }


                    /*console.log('Add Licence');
                    this.$parent.page_title = "Licence";
                    if(!this.lab_id){
                        location.href = '#/add-lab';
                    }else{
                        if(this.$parent.lab_detail == undefined){
                            this.updatelab(this.lab_id);
                        }else{
                            this.previous = true;
                        }
                    }*/
                }
            },
            getLicenceValue: function(){
                this.errors = [{}];
                this.error = "";
                console.log(this.formData);
                this.$http.post('/get_licence_value',this.formData).then((response) => {
                    console.log(response.body);
                    if(response.body.isvalid){
                        if(response.body.success){
                            console.log("successfully updated");
                            this.message = response.body.message;
                        }else{
                            console.log("error in update");
                            this.error = response.body.message;
                        }
                    }else{
                        this.errors = response.body.errors;
                    }
                }, (response) => {

                });
            }
        }
    }
</script>