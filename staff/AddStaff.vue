<template>
<div>
  <form v-if="showPage" id="main_form" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
    <div class="max-form-width div-center form-center full-width">
      <div class="col-md-10 inner-body full-width no-padding">
        <div v-show="showloader" class="text-center loader"><img src="/images/loading.gif"></div>
        <div v-show="error" class="col-sm-12 page-error">
          <div v-bind:class="error.type" class="text-center">{{error.message}}</div>
        </div>
        <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title">STAFF DETAILS </div></div>

        <!-- new row -->
        <div class="col-sm-12">
          <div class="col-md-12 form-group">
            <label class="control-label hidden-xs full-width text-left">Username</label>
            <input v-model="formData.username" placeholder="Username" class="md-input input-with-icon" type="text">
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.username }">{{errors.username?errors.username[0]:''}}
            </div>
          </div>
        </div>
        <!-- row end --> 

        <!-- new row -->
        <div class="col-sm-12">
          <div class="col-md-6 form-group">
            <label class="control-label hidden-xs full-width text-left">Password</label>
            <input v-model="formData.password" placeholder="Password" class="md-input input-with-icon" type="password">
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.password }">{{errors.password?errors.password[0]:''}}
            </div>
          </div>
          <div class="col-md-6 form-group">
            <label class="control-label hidden-xs full-width text-left">Confirm Password</label>
            <input v-model="formData.password_confirmation" placeholder="Confirm Password" class="md-input input-with-icon" type="password">
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.password_confirmation }">{{errors.password_confirmation?errors.password_confirmation[0]:''}}
            </div>
          </div>
        </div>
        <!-- row end -->

        <!-- new row -->
        <div class="row" style="border-bottom: 1px solid #e5e5e5;">
          <div class="col-md-2 modal-title " style="padding:15px;" ><h4 class="modal-title">Roles</h4></div>
          <div class="col-md-4 checkbox " style="padding:10px 10px 10px 15px;" >
            <label><input type="checkbox" id="checkAllCheckbox" v-on:click="selectAll" v-model="checkAll"  /> Check all</label>
          </div>
        </div>
        <!-- row end -->

        <!-- new row -->
        <div class="row">
          <div style="height:200px;overflow-y:scroll;overflow-x:hidden;">
            <div v-for="role in roles" class="col-md-6 modal-title " style="padding:0px 5px 0px 15px;" >
              <div class="checkbox">
                <label>
                  <input name="checkedNames[]" type="checkbox" v-bind:id="role.id" class="user-permissions" v-bind:value="role.id" > {{ role.name }}
                </label>
              </div>
            </div>
          </div>
        </div>
        <!-- row end -->


        <!-- new row -->
        <div class="col-sm-12"  >
          <div class="col-md-12 form-group">
            <label class="control-label hidden-xs full-width text-left">Status</label>
            <div class="col-md-2">Active  : <input name="user_status" id="status_yes" value="1" type="radio" v-model="formData.user_status" ></div>
            <div class="col-md-2">Inactive : <input name="user_status" id="status_no" value="0" type="radio" v-model="formData.user_status"></div>
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.user_status }">{{errors.user_status?errors.user_status[0]:''}}
            </div>
          </div>
        </div>
        <!-- row end -->

        <div class="col-sm-12 form-group text-center footer-buttons">
          <div class="form-group">
            <button v-on:click="BacKtosTaff" type="button" class="btn btn-default rounded-corner">Cancel</button>
          </div>
          <div class="form-group">
            <button type="submit" class="btn btn-primary rounded-corner">Save</button>
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

import unauthorisedVue from '../common/unauthorised.vue';

  export default {
    mounted() {

      this.$parent.showAppPage=false;
      this.$parent.page_title = "";

     /* this.checkAccess();
      this.$parent.page_title = "Add/Edit Staff Info";

      //this.tests=this.$parent.tests;

      this.getRoles();

      if(this.$route.params.id != undefined){

        this.updateRecord(this.$route.params.id);
        //var rec_id=this.$route.params.id;


      }*/


    },


    data: function() {
      return {
        showloader: false,
        errors: {},
        error:{},
        formData:{},
        roles:{},
        checkedRoles:[],
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

        var checkedItems = $("input[name='checkedNames[]']:checked");
        var role_id=[];
        $.map(checkedItems, function(val, key) {
          role_id.push(val.value);
        });
       this.formData.role_id=role_id;

        this.$http.post('/staff',this.formData).then((response) => {

          if(response.body.isvalid){
            if(response.body.success){
              this.error = {'type':'text-success' ,'message':'Updated successfully'};
              this.$parent.staff = {};
              this.showloader =  false;

              this.getRecords();
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


      updateRecord: function(record_id) {
        this.showloader =  true;
        this.$http.get('/staff/'+record_id).then((response) => {
          this.showloader =  false;
          this.formData = response.body.record;
          
          this.checkedRoles= response.body.checkedRoles;
          var checkedRoles = this.checkedRoles;

          setTimeout(function() {
            //this.showloader =  true;
            $(".user-permissions").each(function(){
                if($.inArray(parseInt($(this).val()),checkedRoles)!=-1){

                  $(this).prop("checked",true);
                  $(this).attr("checked","checked");
                }
            });
            //this.showloader =  false;
          }, 1000);

        }, (response) => {
          console.log(response);
        });
      },

      BacKtosTaff: function() {
        location.href = '#/staff';
      },

      getRecords: function() {
        this.$http.get('/staff').then((response) => {
          this.showloader =  false;
          this.formData = {};
          this.$parent.staff = response.body.records;
          location.href = '#/staff';
        }, (response) => {
          console.log(response);
        });
      }, 

      getRoles: function(){

        this.$http.get('/api/staff-roles').then((response) => {
          
            this.roles = response.body.roles;  
         /* if(response.body.inValid==undefined){}else{
            location.href = '#/home';
          }*/
          

        }, (response) => {
          console.log(response);
        });
      },

      selectAll: function(){
        if(this.checkAll==false){
          $("input[name='checkedNames[]']").prop("checked",true);
          $("input[name='checkedNames[]']").attr("checked","checked");
        }else{
          $("input[name='checkedNames[]']").prop("checked",false);
          $("input[name='checkedNames[]']").removeAttr("checked","checked");
        }
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
          this.$parent.page_title = "Add/Edit Staff Info";
          this.$parent.showAppPage=true;  
          this.showPage=true;
          this.getRoles();

          if(this.$route.params.id != undefined){
            this.updateRecord(this.$route.params.id);
          }   

     /* this.checkAccess();
      this.$parent.page_title = "Add/Edit Staff Info";

      //this.tests=this.$parent.tests;

      this.getRoles();

      if(this.$route.params.id != undefined){

        this.updateRecord(this.$route.params.id);
        //var rec_id=this.$route.params.id;


      }
      */

        }
      } 
    }
  }
</script>

