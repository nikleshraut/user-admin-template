<template>
  <div>
  <form  v-if="showPage" id="record_update_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
    <div class="max-form-width div-center form-center full-width">
      <div class="col-md-10 inner-body full-width no-padding">
        <div v-show="showloader" class="text-center loader">
            <img src="/images/loading.gif">
        </div>

        <!-- new row -->
        <div class="row">
        
          <div v-show="error" class="col-sm-12 page-error">
              <div v-bind:class="error.type" class="text-center">
                  {{error.message}}
              </div>
          </div>
        </div>
        <!-- row end -->

        
        <!-- new row -->
        <div class="row">
          <div class="col-sm-12">
            <div class="col-md-2 form-group">
              <label class="control-label hidden-xs full-width text-left">Name</label>
            </div>
            <div class="col-md-10 form-group">
              <input v-model="formData.name" placeholder="Unit Name" class="md-input input-with-icon" type="text">
              <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.name }">{{errors.name?errors.name[0]:''}}
              </div>
            </div>
          </div>
        </div>
        <!-- row end -->
        <!-- new row -->
        <div class="row">
          <div class="col-sm-12">
            <div class="col-md-2 form-group">
              <label class="control-label hidden-xs full-width text-left">Description</label>
            </div>
            <div class="col-md-10 form-group">
              <input v-model="formData.description" placeholder="Description" class="md-input input-with-icon" type="text">
              <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.description }">{{errors.description?errors.description[0]:''}}
              </div>
            </div>
          </div>
        </div>
        <!-- row end -->

        <!-- new row -->
        <div class="row">
          <div class="col-sm-12">
            <div class="col-md-2 form-group">
              <label class="control-label hidden-xs full-width text-left">Status</label>
            </div>
            <div class="col-md-10 form-group">
              <span style="padding-right:10px;">Active : <input name="status" id="status_yes" value="1" type="radio" v-model="formData.status" ></span>
              <span style="padding-right:10px;"><span style="padding-right:10px;">Inactive : <input name="status" id="status_no" value="0" type="radio" v-model="formData.status" ></span>

              <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.status }">        
              {{errors.status?errors.status[0]:''}}
              </div>

              
            </div>
          </div>
        </div>
        <!-- row end -->

        <!-- new row -->
        <div class="row">
          <div class="col-sm-12 form-group text-center footer-buttons">
            <div class="form-group">
            <button v-on:click="backToPage" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
            <div class="form-group">
            <button type="submit" class="btn btn-primary rounded-corner">Save</button></div>
          </div>
        </div>        
        <!-- row end -->

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
        

        
       /* if(this.$route.params.id != undefined){
            this.updateRecord(this.$route.params.id);
            console.log(this.$route.params.id);
        }else{
            //this.applySelect2();
        }*/
    },
    data: function() {
        return {
            showloader: false,
            errors: {},
            error:{},
            formData:{},
            showPage:false,
            valid_access:false,
            rec_id:0,
            
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
            console.log(this.formData);
            this.errors = {};
            this.showloader = true;
            
            this.$http.post('/units',this.formData).then((response) => {
                //this.showloader = false;
                 
                if(response.body.isvalid){
                    if(response.body.success){
                        this.error = {'type':'text-success' ,'message':'Updated successfully'};
                        this.$parent.units = {};
                        this.showloader =  false;
                        this.getRecrods();
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
        setAllUnboundValue: function() {
        },
        updateAllUnboundValue: function() {
        },
       
        updateRecord: function(unit_id) {
            //this.showloader =  true;
            this.$http.get('/units/'+unit_id).then((response) => {
                this.showloader =  false;
                this.formData = response.body.record;
                /*var vm = this;
                setTimeout(function() {
                    vm.applySelect2();
                }, 1);*/
            }, (response) => {
                console.log(response);
            });
        },
        backToPage: function() {
            location.href = '#/units';
        },
        getRecrods: function() {
            this.$http.get('/units').then((response) => {
                this.showloader =  false;
                this.formData = {};
                this.$parent.units = response.body.units;
                location.href = '#/units';
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
          this.$parent.page_title = "Add/Edit Unit Info";
          this.$parent.showAppPage=true;  
          this.showPage=true;
          if(this.$route.params.id != undefined){
            this.updateRecord(this.$route.params.id);
          }              
        }
      }
    }
  }
</script>