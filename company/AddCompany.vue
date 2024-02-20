<template>
<div>
    <form v-if="showPage"  id="test_registration_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
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
                <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title">COMPANY DETAILS </div></div>

                <!-- new row -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Company Name</label>
                        <input v-model="formData.name" placeholder="Company Name" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.clnc_hsptl_name }">{{errors.name?errors.name[0]:''}}
                        </div>
                    </div>
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Branch No</label>
                        <input v-model="formData.branch_no" placeholder="Branch No" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.branch_no }">{{errors.branch_no?errors.branch_no[0]:''}}
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
                        <input v-model="formData.mobile" placeholder="Mobile" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.mobile }">{{errors.mobile?errors.mobile[0]:''}}
                        </div>
                    </div>
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Phone</label>
                        <input v-model="formData.phone" placeholder="Phone" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.phone }">{{errors.phone?errors.phone[0]:''}}
                        </div>
                    </div>
                </div>
                <!-- row end -->

                <div class="col-sm-12 form-group text-center footer-buttons">
                    <div class="form-group">
                    <button v-on:click="AllCompany" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
                    <div class="form-group">
                    <button type="submit" class="btn btn-primary rounded-corner">Save</button></div>
                </div>

            </div>
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
      this.$parent.showAppPage=false;
      this.$parent.page_title = "";

        /*this.$parent.page_title = "Add/Edit Company Info";
        if(this.$route.params.id != undefined){
            this.updateCompany(this.$route.params.id);
        }*/
    },
    data: function() {
        return {
            showloader: false,
            errors: {},
            error:{},
            formData:{qualification:""},
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
      'unauthorisedVue':unauthorisedVue
    }, 

    methods: {
        submitForm: function(){
            this.errors = {};
            this.showloader = true;
            this.$http.post('/company',this.formData).then((response) => {
                //this.showloader = false;
                if(response.body.isvalid){
                    if(response.body.success){
                        this.error = {'type':'text-success' ,'message':'Updated successfully'};
                        this.$parent.company = {};
                        this.showloader =  false;
                        //this.getAllCompanys();
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
        updateCompany: function(company_id) {
            this.showloader =  true;
            this.$http.get('/company/'+company_id).then((response) => {
                this.showloader =  false;
                this.formData = response.body.company;
            }, (response) => {
                console.log(response);
            });
        },
        AllCompany: function() {
            location.href = '#/company';
        },
        getAllCompanys: function() {
            this.$http.get('/company').then((response) => {
                this.showloader =  false;
                this.formData = {};
                this.$parent.company = response.body.company;
                location.href = '#/company';
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
          this.$parent.page_title = "Add/Edit Company Info";
          this.$parent.showAppPage=true;  
          this.showPage=true;
            if(this.$route.params.id != undefined){
                this.updateCompany(this.$route.params.id);
            }    

        /*this.$parent.page_title = "Add/Edit Company Info";
        if(this.$route.params.id != undefined){
            this.updateCompany(this.$route.params.id);
        }
        */                
        }
      }       
    }
  }
</script>