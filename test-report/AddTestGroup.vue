<template>
<div>
  <form  v-if="showPage" id="main_form" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
    <div class="max-form-width div-center form-center full-width">
      <div class="col-md-10 inner-body full-width no-padding">
        <div v-show="showloader" class="text-center loader"><img src="/images/loading.gif"></div>
        <div v-show="error" class="col-sm-12 page-error">
          <div v-bind:class="error.type" class="text-center">{{error.message}}</div>
        </div>
        <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title">TEST GROUP DETAILS </div></div>

        <!-- new row -->
        <div class="col-sm-12">
          <div class="col-md-12 form-group">
            <label class="control-label hidden-xs full-width text-left">Name</label>
            <input v-model="formData.name" placeholder="Test Name" class="md-input input-with-icon" type="text">
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.name }">{{errors.name?errors.name[0]:''}}
            </div>
          </div>
        </div>
        <!-- row end -->

        <!-- new row -->
        <div class="col-sm-12">
          <div class="col-md-6 form-group">
            <label class="control-label hidden-xs full-width text-left">Short Name</label>
            <input v-model="formData.short_name" placeholder="Short Name" class="md-input input-with-icon" type="text">
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.short_name }">{{errors.short_name?errors.short_name[0]:''}}
            </div>
          </div>
          <div class="col-md-6 form-group">
            <label class="control-label hidden-xs full-width text-left">Fee</label>
            <input v-model="formData.fee" placeholder="Fee" class="md-input input-with-icon" type="text">
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.fee }">{{errors.fee?errors.fee[0]:''}}
            </div>
          </div>
        </div>
        <!-- row end -->

        <!-- new row -->
        <div class="row" style="border-bottom: 1px solid #e5e5e5;">
          <div class="col-md-2 modal-title " style="padding:15px;" ><h4 class="modal-title">Tests</h4></div>
          <div class="col-md-4 checkbox " style="padding:10px 10px 10px 15px;" >
            <label><input type="checkbox" id="checkAllCheckbox" v-on:click="selectAll" v-model="checkAll"  /> Check all</label>
          </div>
        </div>
        <!-- row end -->

        <!-- new row -->
        <div class="row">
          <div style="height:200px;overflow-y:scroll;overflow-x:hidden;">
            <div v-for="test in tests" class="col-md-6 modal-title " style="padding:0px 5px 0px 15px;" >
              <div class="checkbox">
                <label>
                  <input name="checkedNames[]" type="checkbox" v-bind:id="test.id" class="user-permissions" v-bind:value="test.id" > {{ test.name }}
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
            <div class="col-md-2">Active  : <input name="status" id="status_yes" value="1" type="radio" v-model="formData.status" ></div>
            <div class="col-md-2">Inactive : <input name="status" id="status_no" value="0" type="radio" v-model="formData.status"></div>
            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.status }">{{errors.status?errors.status[0]:''}}
            </div>
          </div>
        </div>
        <!-- row end -->

        <div class="col-sm-12 form-group text-center footer-buttons">
          <div class="form-group">
            <button v-on:click="AllTestGroups" type="button" class="btn btn-default rounded-corner">Cancel</button>
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

     /* this.$parent.page_title = "Add/Edit Test Group Info";

      //this.tests=this.$parent.tests;

      this.getTests();

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
        tests:{},
        checkedTests:[],
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
        var test_id=[];
        $.map(checkedItems, function(val, key) {
          test_id.push(val.value);
        });
       this.formData.test_id=test_id;

        this.$http.post('/test-groups',this.formData).then((response) => {

          if(response.body.isvalid){
            if(response.body.success){
              this.error = {'type':'text-success' ,'message':'Updated successfully'};
              this.$parent.test_groups = {};
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
        this.$http.get('/test-groups/'+record_id).then((response) => {
          this.showloader =  false;
          this.formData = response.body.test_group;
          this.checkedTests= response.body.checkedTests;
          var checkedTests = this.checkedTests;

          setTimeout(function() {
            //this.showloader =  true;
            $(".user-permissions").each(function(){
                if($.inArray(parseInt($(this).val()),checkedTests)!=-1){

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

      AllTestGroups: function() {
        location.href = '#/test-groups';
      },

      getRecords: function() {
        this.$http.get('/test-groups').then((response) => {
          this.showloader =  false;
          this.formData = {};
          this.$parent.test_groups = response.body.records;
          location.href = '#/test-groups';
        }, (response) => {
          console.log(response);
        });
      },

      getTests: function(){

        this.$http.get('/api/tests').then((response) => {
          this.tests = response.body.tests;

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
          this.$parent.page_title = "Add/Edit Unit Info";
          this.$parent.showAppPage=true;  
          this.showPage=true;
          this.getTests();
          if(this.$route.params.id != undefined){
            this.updateRecord(this.$route.params.id);
          }              
        }
      }
    }
  }
</script>

