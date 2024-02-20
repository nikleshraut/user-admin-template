<template>
 <div style="padding: 10px 0;">


    <!-- Lab Form -->
    <form v-if="showPage" class="form-body" role="form" method="POST" v-on:submit.prevent="submitForm">
        <div v-show="formLoading" class="text-center loader">
            <img src="/images/loading.gif">
        </div>
        <div v-if="error" class="col-sm-12 page-error">
            <div v-bind:class="error.type" class="text-center">
                {{error.message}}
            </div>
        </div>
      <!-- modal-body -->
      <div class="modal-body lab-modal-body">
        <div class="hide" id="modalErrMsg"></div>
        <div class="">
          <div class="row panel">
            <div class="col-md-9"><h4>Update Lab Details</h4></div>
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
                <label for="lab_name" class="col-md-4 control-label align-control-label">Lab Name<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Lab Name" v-model="lab_form.lab_name" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.lab_name }">{{errors.lab_name?errors.lab_name[0]:''}}
                  </div>
                </div>
              </div>
              <div class="col-md-6 form-group lab_reg_no-form-group">
                <label for="lab_reg_no" class="col-md-4 control-label align-control-label">Reg No.<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Registration No" v-model="lab_form.lab_reg_no" value="" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.lab_reg_no }">{{errors.lab_reg_no?errors.lab_reg_no[0]:''}}
                  </div>
                </div>
              </div>
            </div>

            <!-- Ward - Mohalla -->
            <!-- <div class="row">
              <div class="col-md-6 form-group address_ward-form-group">
                <label for="address_ward" class="col-md-4 control-label align-control-label">Ward</label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Ward" v-model="lab_form.address_ward" type="text">
                  <div class="address_ward-error">
                  </div>
                </div>
              </div>
              <div class="col-md-6 form-group address_mohalla-form-group">
                <label for="address_mohalla" class="col-md-4 control-label align-control-label">Mohalla</label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Mohalla" v-model="lab_form.address_mohalla" type="text">
                  <div class="address_mohalla-error">
                  </div>
                </div>
              </div>
            </div> -->


            <!-- Lane - Area -->
            <div class="row">
              <div class="col-md-6 form-group address_lane-form-group">
                <label for="address_lane" class="col-md-4 control-label align-control-label">Address</label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Lane" v-model="lab_form.address" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.address }">{{errors.address?errors.address[0]:''}}
                  </div>
                </div>
              </div>
              <div class="col-md-6 form-group address_area-form-group">
                <label for="address_area" class="col-md-4 control-label align-control-label">Area<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Area" v-model="lab_form.address_area" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.address_area }">{{errors.address_area?errors.address_area[0]:''}}
                  </div>
                </div>
              </div>
            </div>

            <!-- City - Pincode -->
            <div class="row">
              <div class="col-md-6 form-group city-form-group">
                <label for="city" class="col-md-4 control-label align-control-label">City<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="City" v-model="lab_form.city" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.city }">{{errors.city?errors.city[0]:''}}
                  </div>
                </div>
              </div>
              <div class="col-md-6 form-group pincode-form-group">
                <label for="pincode" class="col-md-4 control-label align-control-label">Pincode</label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Pincode" v-model="lab_form.pincode" value="" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.pincode }">{{errors.pincode?errors.pincode[0]:''}}
                  </div>
                </div>
              </div>
            </div>

            <!-- District - Phone -->
            <div class="row">
              <div class="col-md-6 form-group state-form-group">
                <label for="state" class="col-md-4 control-label align-control-label">State<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="District" v-model="lab_form.state" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.state }">{{errors.state?errors.state[0]:''}}
                  </div>
                </div>
              </div>
            </div>


        <!-- Owner Details -->
        <div class="row"><div class="col-md-12 panel"><h4>Owner Details</h4></div></div>

         <!-- First Name - Last Name -->
            <div class="row">
              <div class="col-md-6 form-group owner_firstname-form-group">
                <label for="owner_firstname" class="col-md-4 control-label align-control-label">First Name<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="First Name" v-model="lab_form.owner_firstname" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.owner_firstname }">{{errors.owner_firstname?errors.owner_firstname[0]:''}}
                  </div>
                </div>
              </div>
              <div class="col-md-6 form-group owner_lastname-form-group">
                <label for="owner_lastname" class="col-md-4 control-label align-control-label">Last Name<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Last Name" v-model="lab_form.owner_lastname" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.owner_lastname }">{{errors.owner_lastname?errors.owner_lastname[0]:''}}
                  </div>
                </div>
              </div>
            </div>

            <!-- Designation - Qualification -->
            <div class="row">
              <div class="col-md-6 form-group owner_designation-form-group">
                <label for="owner_designation" class="col-md-4 control-label align-control-label">Designation<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Designation" v-model="lab_form.owner_designation" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.owner_designation }">{{errors.owner_designation?errors.owner_designation[0]:''}}
                  </div>
                </div>
              </div>
              <div class="col-md-6 form-group owner_reg_no-form-group">
                <label for="owner_reg_no" class="col-md-4 control-label align-control-label">GST No</label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Registration No" v-model="lab_form.gst_no" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.gst_no }">{{errors.gst_no?errors.gst_no[0]:''}}
                  </div>
                </div>
              </div>
            </div>

            <!-- Registration No - Contact No -->
            <div class="row">
              <div class="col-md-6 form-group owner_qualification-form-group">
                <label for="owner_qualification" class="col-md-4 control-label align-control-label">Qualification<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <select v-model="lab_form.owner_qualification" class="form-control" tabindex="-1" aria-hidden="true">
                    <option value="">Select Qualification</option>
                    <option v-for="qualification in qualifications"  v-bind:value="qualification.data_key_value"  >{{ qualification.data_key_value }}</option>
                    </select>
                  <div class=" " v-bind:class="{ 'text-danger': errors.owner_qualification }">{{errors.owner_qualification?errors.owner_qualification[0]:''}}
                  </div>
                </div>
              </div>
              <div class="col-md-6 form-group owner_contact_no-form-group">
                <label for="owner_contact_no" class="col-md-4 control-label align-control-label">Contact No<span class="required_field">*</span></label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Contact No" v-model="lab_form.owner_contact_no" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.owner_contact_no }">{{errors.owner_contact_no?errors.owner_contact_no[0]:''}}
                  </div>
                </div>
              </div>
            </div>

            <div class="row">
              <div class="col-md-6 form-group owner_reg_no-form-group" v-show="lab_form.owner_qualification=='M.B.B.S'">
                <label for="owner_reg_no" class="col-md-4 control-label align-control-label">Registration No</label>
                <div class="col-md-8">
                  <input class="form-control" placeholder="Registration No" v-model="lab_form.owner_reg_no" type="text">
                  <div class=" " v-bind:class="{ 'text-danger': errors.owner_reg_no }">{{errors.owner_reg_no?errors.owner_reg_no[0]:''}}
                  </div>
                </div>
              </div>
            </div>

          </div>
        </div>
        <!--/ modal-body -->




    <!-- modal-footer -->
    <div class="modal-footer">
      <input id="record_id" name="record_id" value="" type="hidden">
      <button type="submit" class="btn btn-primary">Submit</button>
      <button v-on:click="backPage" type="button" class="btn btn-default">Cancel</button>

    </div>
    <!--/ modal-footer -->



  </form>
  <!-- Lab Form -->
<!-- {{ ValidPageAccess }}
<unauthorised-vue v-if="!showPage"></unauthorised-vue> -->

</div>
</template>

<script>
  import unauthorisedVue from '../common/unauthorised.vue';

    export default {
        mounted() {
            this.getAllUserQualifications();
            this.getLabDetail();
        },
        data: function() {
          return {
            formLoading: false,
            lab_form:{},
            qualifications:[],
            showPage:true,
            valid_access:false,
            errors: "",
            error: "",
          }
        },
        computed:{
            /*ValidPageAccess: function () {
                if(this.$parent.valid_access==true){
                    if(this.$parent.app_host.sub_domain==this.$parent.app_domains.lab_admin_domain){
                        this.$parent.valid_access=false;
                    }
                }
                this.valid_access=this.$parent.valid_access;
                this.checkAccess();
            }*/
        },
        components: {
          'unauthorisedVue':unauthorisedVue
        },
        methods:{
          getAllUserQualifications:function(){
            this.$http.get('/api/user_qualifications').then((response) => {
            this.qualifications= response.body.qualifications;
            this.$parent.user_qualifications = response.body.qualifications;
            }, (response) => {
            console.log(response);
            });
          },
          getLabDetail: function(){
                this.formLoading = true;
                var lab_id = this.$route.params.id;
                this.$http.get('/api/pathology/'+lab_id).then((response) => {
                    this.lab_form = response.body.lab_detail;
                    this.formLoading = false;
                }, (response) => {
                    console.log(response);
                });
            },
            submitForm: function(){
                this.formLoading = true;
                this.$http.post('/api/pathologies',this.lab_form).then((response) => {
                    this.formLoading = false;
                    if(response.body.isvalid){
                        if(response.body.success){
                            this.lab_form = {};
                            this.error = {type:"success",message:"Lab successfully updated."};
                        }else{
                            this.error = {type:"danger",message:response.body.message};
                        }
                    }else{
                        this.errors = response.body.errors;
                    }
                }, (response) => {
                    this.error = {type:"danger",message:response.body.message};
                });
            },
            backPage: function(){
                location.href = '#/my-pathology';
            },
            /*checkAccess: function(){
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
                }
            }*/
        }

    }
</script>