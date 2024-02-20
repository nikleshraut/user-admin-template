<template>
 <div style="padding: 10px 0;">


    <!-- Lab Form -->
    <form v-if="showPage" class="form-body" role="form" method="POST" >
      <div v-show="formLoading" class="text-center"><img src="/images/loading.gif" style=""></div>
      <!-- modal-body -->
      <div v-show="!formLoading" class="modal-body lab-modal-body">
        <div class="hide" id="modalErrMsg"></div>
        <div class="">
          <div class="row panel">
            <div class="col-md-9"><h4>Lab Details</h4></div>
            <div class="col-md-3 pull-right padding-tb">&nbsp;</div>
          </div>
          <!-- Sub-Domain - Approved Date -->
          <div class="row">
            <div class="col-md-6 form-group lab_host_name-form-group">
              <label for="lab_host_name" class="col-md-4 control-label align-control-label">Sub-Domain </label>
              <div class="col-md-8 lab_host_name-added" style="display: none;"></div>
              <div class="col-md-8 lab_host_name-new">{{ lab_form.lab_host_name}}</div>
            </div>
          </div>
          <!-- Lab Name - Registration No -->
          <div class="row">
            <div class="col-md-6 form-group lab_name-form-group">
              <label for="lab_name" class="col-md-4 control-label align-control-label">Lab Name </label>
              <div class="col-md-8">{{ lab_form.lab_name }}</div>
            </div>
            <div class="col-md-6 form-group lab_reg_no-form-group">
              <label for="lab_reg_no" class="col-md-4 control-label align-control-label">Reg No. </label>
              <div class="col-md-8">{{ lab_form.lab_reg_no }}</div>
            </div>
          </div>
          <!-- Ward - Mohalla -->
          <div class="row">
            <div class="col-md-6 form-group address_ward-form-group">
              <label for="address_ward" class="col-md-4 control-label align-control-label">Ward</label>
              <div class="col-md-8">{{ lab_form.address_ward }} </div>
            </div>
            <div class="col-md-6 form-group address_mohalla-form-group">
              <label for="address_mohalla" class="col-md-4 control-label align-control-label">Mohalla</label>
              <div class="col-md-8"> {{ lab_form.address_mohalla }}</div>
            </div>
          </div>
          <!-- Lane - Area -->
          <div class="row">
            <div class="col-md-6 form-group address_lane-form-group">
              <label for="address_lane" class="col-md-4 control-label align-control-label">Lane</label>
              <div class="col-md-8">{{ lab_form.address_lane }}</div>
            </div>
            <div class="col-md-6 form-group address_area-form-group">
              <label for="address_area" class="col-md-4 control-label align-control-label">Area </label>
              <div class="col-md-8"> {{ lab_form.address_area }}</div>
            </div>
          </div>
        </div>
        <!-- City - Pincode -->
        <div class="row">
          <div class="col-md-6 form-group city-form-group">
            <label for="city" class="col-md-4 control-label align-control-label">City </label>
            <div class="col-md-8"> {{ lab_form.city }}</div>
          </div>
          <div class="col-md-6 form-group pincode-form-group">
            <label for="pincode" class="col-md-4 control-label align-control-label">Pincode</label>
            <div class="col-md-8">{{ lab_form.pincode }}</div>
          </div>
        </div>
        <!-- District - Phone -->
        <div class="row">
          <div class="col-md-6 form-group district-form-group">
            <label for="district" class="col-md-4 control-label align-control-label">District </label>
            <div class="col-md-8">{{ lab_form.district }}</div>
          </div>
          <div class="col-md-6 form-group lab_phone-form-group">
            <label for="lab_phone" class="col-md-4 control-label align-control-label">Phone</label>
            <div class="col-md-8"> {{ lab_form.lab_phone  }}</div>
          </div>
        </div>
        <!-- Owner Details -->
        <div class="row"><div class="col-md-12 panel"><h4>Owner Details</h4></div></div>
        <!-- First Name - Last Name -->
        <div class="row">
          <div class="col-md-6 form-group owner_firstname-form-group">
            <label for="owner_firstname" class="col-md-4 control-label align-control-label">First Name </label>
            <div class="col-md-8"> {{ lab_form.owner_firstname }}</div>
          </div>
          <div class="col-md-6 form-group owner_lastname-form-group">
            <label for="owner_lastname" class="col-md-4 control-label align-control-label">Last Name </label>
            <div class="col-md-8"> {{ lab_form.owner_lastname }}</div>
          </div>
        </div>
        <!-- Designation - Qualification -->
        <div class="row">
          <div class="col-md-6 form-group owner_designation-form-group">
            <label for="owner_designation" class="col-md-4 control-label align-control-label">Designation </label>
            <div class="col-md-8"> {{ lab_form.owner_designation }}</div>
          </div>
          <div class="col-md-6 form-group owner_qualification-form-group">
            <label for="owner_qualification" class="col-md-4 control-label align-control-label">Qualification </label>
            <div class="col-md-8">{{ lab_form.owner_qualification }} </div>
          </div>
        </div>
        <!-- Registration No - Contact No -->
        <div class="row">
          <div class="col-md-6 form-group owner_reg_no-form-group">
            <label for="owner_reg_no" class="col-md-4 control-label align-control-label">Registration No</label>
            <div class="col-md-8"> {{ lab_form.owner_reg_no }}</div>
          </div>
          <div class="col-md-6 form-group owner_contact_no-form-group">
            <label for="owner_contact_no" class="col-md-4 control-label align-control-label">Contact No </label>
            <div class="col-md-8"> {{ lab_form.owner_contact_no }} </div>
          </div>
        </div>
      </div>


    <!-- modal-footer -->
    <div v-show="!formLoading" class="modal-footer">
      <input id="record_id" name="record_id" value="" type="hidden">
      <button v-on:click="backPage" type="button" class="btn btn-default">Back</button>

    </div>
    <!--/ modal-footer -->



  </form>
  <!-- Lab Form -->
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

            /*console.log('View Lab');
            this.getLabDetail();*/
        },
        data: function() {
          return {
            formLoading: true,
            lab_form:{},
            showPage:false,
            valid_access:false,

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
            backPage: function(){
                location.href = '#/my-pathology';
            },
            getLabDetail: function(){
                var lab_id = this.$route.params.id;
                this.$http.get('/api/pathology/'+lab_id).then((response) => {
                    this.lab_form = response.body.lab_detail;
                    this.formLoading = false;
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
                this.$parent.page_title = "View Lab";
                //this.$parent.back_url = "#/";
                //this.$parent.help_content = "<h4>My Pathology</h4> help content";

                this.$parent.showAppPage=true;  
                this.showPage=true;
                this.getLabDetail();

                          

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