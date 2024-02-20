<template>
<div>


    <form  v-if="showPage" id="test_registration_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
        <div class="max-form-width div-center form-center full-width">
            <div class="col-md-10 inner-body full-width no-padding">
                <div v-show="formLoading" class="text-center">
                    <img src="/images/loading.gif" style="">
                </div>
                <div class="progress col-sm-12 no-padding">
                  <div class="lab-progress-bar lab-progress-bar-active" role="progressbar" style="width:50%">
                    1. Lab Info
                  </div>
                  <!-- <div class="progress-bar" role="progressbar" style="width:33.33%">
                    2. Lab Users
                  </div> -->
                  <div class="lab-progress-bar" role="progressbar" style="width:50%">
                    2. Licence
                  </div>
                </div>
                <!-- <div class="col-sm-12" v-if="!is_lab_id">
                  <div v-on:click="addLabUser" class="col-sm-3 col-sm-offset-9">
                    <button type="button" class="btn btn-default col-sm-12">Next &gt;&gt;</button>
                  </div>
                </div> -->
                <div v-show="showloader" class="text-center loader">
                    <img src="/images/loading.gif">
                </div>
                <div v-if="error" class="col-sm-12 page-error">
                    <div v-bind:class="error.type" class="text-center">
                        {{error.message}}
                    </div>
                </div>
                <!-- modal-body -->
                <div v-show="!formLoading" class="">
                  <div class="hide" id="modalErrMsg"></div>
                    <div class="">
                        <div class="col-sm-12">
                            <div class="col-md-9"><h4>Lab Details</h4></div>
                            <div class="col-md-3 pull-right padding-tb text-center"><span class="required_field"> *</span> denotes required field</div>
                        </div>
                        <div class="col-sm-12 no-padding"><hr width="100%" /></div>

                        <!-- Sub-Domain - Approved Date -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                               
                                <input v-model="lab_form.lab_host_name"  v-on:change="checkHostName" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.lab_host_name }" type="text">
                                 <label class="control-label text-left">Sub-Domain<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.lab_host_name }">{{errors.lab_host_name?errors.lab_host_name[0]:''}}
                                </div>
                                <div v-show="subdomain_loader" style="position: absolute;top: 0;padding-left: 50%;width: 100%;z-index:99;"><img src="/images/loading.gif" style="width: 70px;"></div>

                                <div v-if="available_domain == true" class="text-success" style="position: absolute;top: 30px;right: 15px;">
                                    <span class="" style="border: 1px solid #57bf57;padding: 4px;border-radius: 29px;color: #57bf57;"><i style="width: 16px;top: 2px;left: 2px;" class="glyphicon glyphicon-ok"></i></span>
                                </div>
                                <div v-if="available_domain == false" class="text-danger" style="position: absolute;top: 30px;right: 15px;">
                                    <span class="" style="border: 1px solid #ee515a;padding: 4px;border-radius: 29px;color: #ee515a;"><i style="width: 16px;top: 2px;left: 2px;" class="glyphicon glyphicon-remove"></i></span>
                                </div>
                            </div>
                            <div class="col-md-6 form-group hidden-xs">
                                &nbsp;
                            </div>
                        </div>
                        <!-- Lab Name - Registration No -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                                
                                <input v-model="lab_form.lab_name" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.lab_name }" type="text">
                                <label class="control-label text-left">Lab Name<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.lab_name }">{{errors.lab_name?errors.lab_name[0]:''}}
                                </div>
                            </div>
                            <div class="col-md-6 form-group md-form-group float-label">
                                
                                <input v-model="lab_form.lab_reg_no" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.lab_reg_no }" type="text">
                                <label class="control-label text-left">Reg No.<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.lab_reg_no }">{{errors.lab_reg_no?errors.lab_reg_no[0]:''}}
                                </div>
                            </div>
                        </div>
                        <!-- Ward - Mohalla -->
                        <!-- <div class="col-sm-12">
                            <div class="col-md-6 form-group">
                                <label class="control-label hidden-xs text-left">Ward</label>
                                <input v-model="lab_form.address_ward" placeholder="Ward" class="md-input input-with-icon" type="text">
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.address_ward }">{{errors.address_ward?errors.address_ward[0]:''}}
                                </div>
                            </div>
                            <div class="col-md-6 form-group">
                                <label class="control-label hidden-xs text-left">Mohalla</label>
                                <input v-model="lab_form.address_mohalla" placeholder="Mohalla" class="md-input input-with-icon" type="text">
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.address_mohalla }">{{errors.address_mohalla?errors.address_mohalla[0]:''}}
                                </div>
                            </div>
                        </div> -->
                        <!-- Lane - Area -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                                
                                <input v-model="lab_form.address" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.address }" type="text">
                                <label class="control-label text-left">Address</label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.address }">{{errors.address?errors.address[0]:''}}
                                </div>
                            </div>
                            <div class="col-md-6 form-group md-form-group float-label">
                                <input v-model="lab_form.address_area" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.address_area }" type="text">
                                <label class="control-label text-left">Area<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.address_area }">{{errors.address_area?errors.address_area[0]:''}}
                                </div>
                            </div>
                        </div>
                        <!-- City - Pincode -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                                
                                <input v-model="lab_form.city" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.city }" type="text">
                                <label class="control-label text-left">City<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.city }">{{errors.city?errors.city[0]:''}}
                                </div>
                            </div>
                            <div class="col-md-6 form-group md-form-group float-label">
                               
                                <input v-model="lab_form.pincode" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.pincode }" type="text">
                                 <label class="control-label text-left">Pincode</label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.pincode }">{{errors.pincode?errors.pincode[0]:''}}
                                </div>
                            </div>
                        </div>
                        <!-- District - Phone -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                                <input v-model="lab_form.state" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.state }" type="text">
                                <label class="control-label text-left">State<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.state }">{{errors.state?errors.state[0]:''}}
                                </div>
                            </div>
                        </div>

                        <!-- Owner Details -->
                        <div class="col-sm-12 no-padding">
                            <div class="col-md-12"><h4>Owner Details </h4></div>
                        </div>
                        <div class="col-sm-12 no-padding"><hr width="100%" /></div>
                        <!-- First Name - Last Name -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                                <input v-model="lab_form.owner_firstname" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.owner_firstname }" type="text">
                                <label class="control-label text-left">First Name<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.owner_firstname }">{{errors.owner_firstname?errors.owner_firstname[0]:''}}
                                </div>
                            </div>
                            <div class="col-md-6 form-group md-form-group float-label">
                                <input v-model="lab_form.owner_lastname" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.owner_lastname }" type="text">
                                <label class="control-label text-left">Last Name<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.owner_lastname }">{{errors.owner_lastname?errors.owner_lastname[0]:''}}
                                </div>
                            </div>
                        </div>
                        <!-- Designation - Qualification -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                                <input v-model="lab_form.owner_designation" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.owner_designation }" type="text">
                                <label class="control-label text-left">Designation<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.owner_designation }">{{errors.owner_designation?errors.owner_designation[0]:''}}
                                </div>
                            </div>
                            <div class="col-md-6 form-group md-form-group float-label">
                                <input v-model="lab_form.gst_no" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.gst_no }" type="text">
                                <label class="control-label text-left">GST No</label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.gst_no }">{{errors.gst_no?errors.gst_no[0]:''}}
                                </div>
                            </div>

                        </div>
                        <!-- Registration No - Contact No -->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label">
                                
                                <input v-model="lab_form.owner_contact_no" placeholder="" class="md-input input-with-icon" v-bind:class="{ 'has-value': lab_form.owner_contact_no }" type="text">
                                <label class="control-label text-left">Contact No<span class="required_field">*</span></label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.owner_contact_no }">{{errors.owner_contact_no?errors.owner_contact_no[0]:''}}
                                </div>
                            </div>
                            <div class="col-md-6 form-group">
                                <label class="control-label text-left">Qualification<span class="required_field">*</span></label>
                                <div class="pos-relative qualification-top">
                                    <select v-model="lab_form.owner_qualification" id="owner_qualification" class="form-control">
                                        <option value="">Select Qualification</option>
                                        <option v-for="qualification in qualifications"  v-bind:value="qualification.data_key_value" >{{ qualification.data_key_value }}</option>
                                    </select>
                                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.owner_qualification }">{{errors.owner_qualification?errors.owner_qualification[0]:''}}
                                    </div>
                                </div>
                            </div>
                        </div>
                        <!-- GST No.-->
                        <div class="col-sm-12">
                            <div class="col-md-6 form-group md-form-group float-label" v-show="lab_form.owner_qualification=='M.B.B.S'">
                               
                                <input v-model="lab_form.owner_reg_no" placeholder="" class="md-input input-with-icon" type="text">
                                 <label class="control-label text-left">Registration No</label>
                                <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.owner_reg_no }">{{errors.owner_reg_no?errors.owner_reg_no[0]:''}}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <!--/ modal-body -->
                <div class="col-sm-12 form-group text-center footer-buttons">
                    <div class="form-group">
                    <button v-on:click="allLabs" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
                    <div class="form-group">
                    <button type="submit" class="btn btn-primary rounded-corner">Submit</button></div>
                </div>
            </div>{{getQualifications}}
        </div>
    </form>
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

            console.log('Add Lab');
            this.$parent.page_title = "Lab Info";
            this.$parent.back_url = "#/my-pathology";
            if(!$.isEmptyObject(this.$parent.lab_detail)){
                this.lab_form = this.$parent.lab_detail;
                //this.is_lab_id = false;
            }else{
                //this.demoFill();
            }
            this.applySelect2();
        },
        data: function() {
            return {
                isloading: true,
                formLoading: false,
                labs: [],
                lab_form: {owner_qualification:'',lab_name:'',lab_reg_no:'',address:'',address_area:'',city:'',pincode:'',state:'',owner_firstname:'',owner_lastname:'',owner_designation:'',gst_no:'',owner_contact_no:'',owner_reg_no:''},
                qualifications: this.$parent.qualifications,
                //is_lab_id: true,
                showloader: false,
                errors: "",
                error: "",
                available_domain: undefined,
                showPage:true,
                valid_access:false,
                subdomain_loader:false,
                demo_count:1,
            }
        },
        computed:{
            getQualifications: function(){
                this.qualifications = this.$parent.qualifications;
            },

            ValidPageAccess: function () {
                //alert("valid_access :0: "+ this.valid_access);
                //valid_access :0: false
                if(this.$parent.valid_access==true){
                    if(this.$parent.app_host.sub_domain==this.$parent.app_domains.lab_admin_domain){
                        this.$parent.valid_access=false;
                    }
                }
                this.valid_access=this.$parent.valid_access;
                //alert("valid_access :1: "+ this.valid_access);
                //valid_access :1: true
                this.checkAccess();
            }

        },
        components: {
          'unauthorisedVue':unauthorisedVue
        },
        methods:{
            addLabUser: function(){
                   location.href = '#/add-lab-user';
            },


            checkHostName:function(){
                /*
                Return domain name status
                1 - Available
                2 - Invalid domain name
                3 - Already exits
                */
                if((this.lab_form.lab_host_name !== undefined)
                   && this.lab_form.lab_host_name !== ''){
                    this.subdomain_loader = true;
                    if(this.lab_form.lab_host_name.length>=4){
                        this.$http.get('/api/check_lab_domain/'+this.lab_form.lab_host_name).then((response) => {
                            this.subdomain_loader = false;
                            this.available_domain=response.body.available_domain;
                            if(!this.available_domain){
                                //this.lab_form.lab_host_name="";
                            }
                        }, (response) => {
                            console.log(response);
                        });
                    }
                }else{
                    this.available_domain = undefined;
                }
            },
            submitForm: function(){
                this.showloader =  true;
                this.$http.post('/api/pathologies',this.lab_form).then((response) => {
                    this.showloader =  false;
                    if(response.body.isvalid){
                        if(response.body.success){
                            this.lab_form = {};
                            this.$parent.lab_id = response.body.lab_id;
                            console.log("successfully updated");
                            //location.href = '#/add-lab-user';
                            location.href = '#/add-licence';
                        }else{
                            console.log("error in update");
                        }
                    }else{
                        this.errors = response.body.errors;
                    }
                }, (response) => {
                    console.log(response);
                });
            },
            updatelab: function(lab_id){
                this.formLoading = true;
                this.lab_form = {};
                if(!this.$parent.lab_detail){
                    this.$http.get('/api/pathology/'+lab_id).then((response) => {
                        this.lab_form = response.body.lab_detail;
                        this.lab_form.id = lab_id;
                        this.$parent.lab_detail = this.lab_form;
                        this.formLoading = false;
                    }, (response) => {
                        console.log(response);
                    });
                }else{
                    this.lab_form = this.$parent.lab_detail;
                }
            },
            allLabs: function(){
                location.href = '#/my-pathology';
            },
            applySelect2: function(){
                var vm = this;
                $("#owner_qualification")
                .select2({ data: this.options })
                .on('change', function () {
                    //console.log($(this).val() + " selected");
                    vm.lab_form.owner_qualification = $(this).val();
                });
                $(".select2-container").addClass("form-select2 text-left").css('width','100%').css('max-width','400px');
                $(".select2-selection").css("border","none");
                $(".select2-selection__rendered").css("padding","0px");
                if(this.lab_form.owner_qualification != undefined){
                    $("#owner_qualification").val(this.lab_form.owner_qualification).change();
                }
            },

            checkAccess: function(){

                if(this.valid_access==''){

                    //this.$parent.validAccess();
                }
                //alert(this.valid_access);
                if(this.valid_access==true ){
                    this.showloader =  false;
                    this.$parent.page_title = "Lab Info";
                    this.$parent.back_url = "#/my-pathology";


                    this.$parent.showAppPage=true;
                    this.showPage=true;

                    if(!$.isEmptyObject(this.$parent.lab_detail)){
                        this.lab_form = this.$parent.lab_detail;
                        //this.is_lab_id = false;

                    }
                    this.applySelect2();

                    /*console.log('Add Lab');
                    this.$parent.page_title = "Lab Info";
                    this.$parent.back_url = "#/my-pathology";
                    if(!$.isEmptyObject(this.$parent.lab_detail)){
                        this.lab_form = this.$parent.lab_detail;
                        this.is_lab_id = false;

                    }
                    this.applySelect2();
                    */
                }

            },
            demoFill: function(){
                var count = this.demo_count;
                this.lab_form = {
                    lab_host_name :"newlab"+this.demo_count,
                    lab_name :"New Lab"+this.demo_count,
                    lab_reg_no : "3"+this.demo_count+"4"+this.demo_count+"5"+this.demo_count,
                    address :"Address"+this.demo_count,
                    address_area :"area"+this.demo_count,
                    city :"city"+this.demo_count,
                    pincode :"123465",
                    state :"MH",
                    owner_firstname : "owner"+this.demo_count,
                    owner_lastname : "owner"+this.demo_count,
                    owner_designation : "test",
                    owner_qualification : "M.B.B.S",
                    owner_reg_no : "11"+(this.demo_count+this.demo_count)+"22",
                    owner_contact_no : "1234567890",
                    gst_no : "1234567890",
                }
            }
        }
    }
</script>