<template>
    <!-- style="height: auto;overflow: auto;" -->
    <form id="test_registration_page" class="form-body" role="form" method="POST" v-on:submit.prevent="submitForm">
        <div class="col-md-10 col-md-offset-1 no-padding" style="background-color:#fff;">
            <div class="col-md-12 inner-body full-width no-padding">
                <div v-show="showloader" class="text-center loader">
                    <img src="/images/loading.gif">
                </div>

                <div v-show="error" class="col-sm-12 page-error">
                    <div v-bind:class="error.type" class="text-center">
                        {{error.message}}
                    </div>
                </div>
                <!-- Test no. and Date created -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Test No</label>
                        <div class="pos-relative">
                            <input v-model="formData.test_no" placeholder="" class="md-input input-with-icon" type="text">
                            <span class="right-input-btn">
                                <button v-on:click="setTestNo" type="button" class="btn btn-primary btn-radius btn-shriked">GENERATE</button>
                            </span>
                            <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors.test_no }">{{errors.test_no?errors.test_no[0]:''}}
                            </div>
                        </div>
                    </div>
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Date</label>
                        <!-- <div class="pos-relative"> -->
                            <input v-model="formData.invoice_date" id="invoice_date" placeholder="" class="md-input input-with-icon" type="text">
                            <!-- <i class="glyphicon glyphicon-calendar"></i> -->
                            <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors.invoice_date }">{{errors.invoice_date?errors.invoice_date[0]:''}}
                            </div>
                        <!-- </div> -->
                    </div>
                </div>
                <!-- row end -->
                <!-- <div class="col-sm-12 no-padding"><hr width="100%" /></div> -->
                <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title page-sub-heading">PATIENT DETAILS </div></div>
                <!-- Name and gender -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Customer Name</label>
                        <input v-model="patient.first_name" id="first_name" placeholder="" class="md-input" type="text">
                        <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors['patient.first_name'] }">{{errors['patient.first_name']?errors['patient.first_name'][0]:''}}
                        </div>
                    </div>
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">&nbsp;</label>
                        <div class="pos-relative">
                            <select v-model="patient.gender" id="gender" name="gender" class="form-control">
                                <option></option>
                                <option value="male">Male</option>
                                <option value="female">Female</option>
                            </select>
                            <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors['patient.gender'] }">{{errors['patient.gender']?errors['patient.gender'][0]:''}}
                            </div>
                        </div>
                    </div>

                    <!--
                    <div class="col-md-2 form-group">
                        <label class="control-label hidden-xs full-width text-left">Customer Name</label>
                        <div class="pos-relative">
                            <select v-model="patient.prefix" id="prefix" name="prefix" class="form-control">
                                <option value="">Select Prefix</option>
                                <option value="dr">Dr.</option>
                                <option value="mr">Mr.</option>
                                <option value="mrs">Mrs.</option>
                            </select>
                            <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['patient.prefix'] }">{{errors['patient.prefix']?errors['patient.prefix'][0]:''}}
                            </div>
                        </div>
                    </div>
                    <div class="col-md-4 form-group">
                        <label class="control-label hidden-xs full-width text-left">&nbsp;</label>
                        <input v-model="patient.last_name" id="last_name" placeholder="Last Name" class="md-input" type="text">
                        <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['patient.last_name'] }">{{errors['patient.last_name']?errors['patient.last_name'][0]:''}}
                        </div>
                    </div> -->
                </div>
                <!-- Age and DOB -->
                <div class="col-sm-12">
                    <div class="col-md-2 form-group">
                        <label class="control-label hidden-xs full-width text-left">Age</label>
                        <input v-model="patient.age_year" id="age_year" placeholder="" class="md-input input-with-icon" type="text">
                        <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors['patient.age_year'] }">{{errors['patient.age_year']?errors['patient.age_year'][0]:''}}
                        </div>
                    </div>
                    <!-- <div class="col-md-2 form-group">
                        <label class="control-label hidden-xs full-width text-left">&nbsp;</label>
                        <input v-model="patient.age_month" id="age_month" placeholder="Month" class="md-input input-with-icon" type="text">
                        <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['patient.age_month'] }">{{errors['patient.age_month']?errors['patient.age_month'][0]:''}}
                        </div>
                    </div>
                    <div class="col-md-2 form-group">
                        <label class="control-label hidden-xs full-width text-left">&nbsp;</label>
                        <input id="idDob" v-model="patient.isDob" class="" type="checkbox">
                        <span> DOB </span>
                    </div>
                    <div class="col-md-6 form-group" v-show="patient.isDob">
                        <label class="control-label hidden-xs full-width text-left">DOB</label>
                        <div class="pos-relative">
                            <input v-on:change="setAge" v-model="patient.dob" id="dob" placeholder="Date of Birth" class="md-input input-with-icon" type="text">
                            <i class="glyphicon glyphicon-calendar"></i>
                            <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['patient.dob'] }">{{errors['patient.dob']?errors['patient.dob'][0]:''}}
                            </div>
                        </div>
                    </div>
                </div> -->
                <!-- row end -->
                <!-- Mobile and Email -->
                <!-- <div class="col-sm-12"> -->
                <div class="col-sm-5 form-group">
                    <div class="col-md-2 no-padding">
                        <label class="control-label hidden-xs full-width text-left">Mobile</label>
                        <div class="pos-relative">
                            <select v-model="patient.country_code" id="country_code" name="country_code" class="form-control">
                                <option value="91">+91</option>
                                <option value="92">+92</option>
                                <option value="1">+1</option>
                            </select>
                            <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors.country_code }">{{errors.country_code?errors.country_code[0]:''}}
                            </div>
                        </div>
                    </div>
                    <div class="col-md-10 no-padding">
                        <label class="control-label hidden-xs full-width text-left">&nbsp;</label>
                        <input v-model="patient.mobile" id="mobile" placeholder="" class="md-input" type="text">
                        <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['patient.mobile'] }">{{errors['patient.mobile']?errors['patient.mobile'][0]:''}}
                        </div>
                    </div>
                </div>
                    <div class="col-md-5 form-group">
                        <label class="control-label hidden-xs full-width text-left">Email</label>
                        <input v-model="patient.email" id="email" placeholder="" class="md-input" type="text">
                        <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors['patient.email'] }">{{errors['patient.email']?errors['patient.email'][0]:''}}
                        </div>
                    </div>
                </div>
                <!-- <div class="col-sm-12">
                    <div class="col-sm-4" style="line-height:100px">
                        <button data-toggle="modal" data-target="#searchPatient" type="button" class="btn btn-primary rounded-corner text-center form-group" style="padding: 5px 15px;" v-on:click="getPatient">Pathonet Id</button>
                    </div>
                    <div class="col-sm-8 pos-relative">
                        <div style="border:1px solid #eee;line-height:100px;height: 100px;"></div>
                        <span style="position: absolute;top: -22px;left: 40px;background-color: #fff;padding: 10px;">Pathonet ID History</span>
                    </div>
                </div> -->
                <div class="col-sm-12 no-padding separator"><hr width="100%" /></div>

                <!-- Test no. and Date created -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <!-- <div class="col-sm-12 no-padding form-group page-sub-title">
                            <div class="col-xs-4 no-padding">REFERED BY</div>
                            <div class="col-xs-4 text-right no-padding">
                                <button v-on:click="addDoctorCalled" data-toggle="modal" data-target="#addDoctor" type="button" class="btn btn-primary rounded-corner" style="padding: 3px 8px;">Add Doctor</button>
                            </div>
                            <div class="col-xs-4 text-right no-padding">
                                Self : <input v-on:change="checkSelfRef" type="checkbox" v-model="formData.selfReference">
                            </div>
                        </div> -->
                        <!-- <div class="col-sm-12 no-padding"> -->
                            <select v-model="formData.doctor_id" id="doctor_id" class="form-control">
                                <option></option>
                                <option value=""></option>
                                <template v-for="doctor in doctor_id_list">
                                    <option v-bind:value="doctor.id">{{doctor.name}}</option>
                                </template>
                            </select>
                            <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors['doctor_id'] }">{{errors['doctor_id']?errors['doctor_id'][0]:''}}
                            </div>
                        <!-- </div> -->
                        <!-- <div class="row">
                            <div class="col-sm-6">
                                Clinic/Hospital Name:
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{ doctor_id.clnc_hsptl_name?doctor_id.clnc_hsptl_name:'' }}
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-sm-6">
                                Qualification
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{ doctor_id.qualification?doctor_id.qualification:'' }}
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-sm-6">
                                Reg.No.
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{ doctor_id.reg_no?doctor_id.reg_no:'' }}
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-sm-6">
                                Address
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{ doctor_id.address?doctor_id.address+" "+doctor_id.city:'' }}
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-sm-6">
                                Mobile
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{ doctor_id.contact_no?doctor_id.contact_no:'' }}
                            </div>
                        </div> -->
                    </div>
                    <div class="col-md-6 form-group">
                        <!-- <div class="form-group page-sub-title">INSURANCE / COMPANY DETAILS</div> -->
                        <select v-model="formData.company_id" id="company_id" class="form-control col-sm-4">
                            <option value=""></option>
                            <template v-for="company_id in company_id_list">
                                <option v-bind:value="company_id.id">{{company_id.name}}</option>
                            </template>
                        </select>
                        <!-- <div class="row">
                            <div class="col-sm-6">
                                Branch No:
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{company_id.branch_no?company_id.branch_no:''}}
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-sm-6">
                                Address
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{company_id.address?company_id.address:''}}
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-sm-6">
                                Mobile
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{company_id.mobile?company_id.mobile:''}}
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-sm-6">
                                Phone
                            </div>
                            <div class="col-sm-6 text-bold">
                                {{company_id.phone?company_id.phone:'/'}}
                            </div>
                        </div> -->
                    </div>
                </div>
                <div class="col-sm-12 no-padding separator"><hr width="100%" /></div>
                <div class="col-sm-12">
                    <div class="col-sm-6">
                        <div class="form-group page-sub-title page-sub-heading">
                            TEST DETAILS
                        </div>
                    </div>
                    <div class="col-sm-6">
                        <div class="form-group page-sub-title page-sub-heading">
                            PRICE
                        </div>
                    </div>
                </div>
                <div v-if="test_selected.length==0">
                    <div class="no-padding col-md-12" v-bind:class="{ 'text-danger': errors['test_detail.0.id'] }">{{errors['test_detail.0.id']?errors['test_detail.0.id'][0]:''}}
                    </div>
                </div>
                <template v-for="(ts,index) in test_selected">
                    <div class="col-sm-12 test-details" v-bind:class="'row_'+index">
                        <div class="col-md-6 form-group">
                            <select v-model="ts.id" class="form-control applySelect2" v-bind:id="index" style="display:none;">
                                <option value="">Select Test</option>
                                <template v-for="test in test_list">
                                    <option v-bind:selected="ts.id==test.id" v-bind:value="test.id">{{test.name}}</option>
                                </template>
                            </select>
                            <div class="no-padding col-md-12" v-bind:class="{ 'text-danger': errors['test_detail.'+index+'.id'] }">{{errors['test_detail.'+index+'.id']?errors['test_detail.'+index+'.id'][0]:''}}
                            </div>
                        </div>
                        <div class="col-md-6 form-group remove-test">
                            <input placeholder="Enter Price" class="md-input input-with-icon" type="text" v-model="ts.price">
                            <i class="pon-close pon-close-dims" v-on:click="removeLine(index)" role="button"></i>
                        </div>
                    </div>
                </template>
                <div class="col-sm-12">
                    <div class="col-sm-12 form-group page-sub-title text-primary"><span v-on:click="addLine" role="button"><strong>+ ADD ANOTHER LINE</strong></span></div>
                </div>

                <div class="col-sm-12 no-padding separator"><hr width="100%" /></div>
                <div class="col-sm-12">
                    <div class="col-sm-12 form-group page-sub-title page-sub-heading">ATTATCHMENTS</div>
                    <div class="col-md-2 form-group text-center" style="height: 100px;line-height: 100px;">
                        <img v-if="temp_image" id="temp_image" class="">
                        <i v-if="!temp_image" class="glyphicon glyphicon-file" style="font-size: 5em;line-height: 100px;"></i>
                    </div>
                    <div class="col-md-10 form-group no-padding">
                        <div class="full-width col-sm-12 text-center attachment">
                            <label for="file-upload" class="custom-file-upload">
                                <span class="btn-primary btn-radius"><i class="glyphicon glyphicon-file"></i></span>
                                Browse your Computer
                            </label>
                            <input class="full-width pointer" value="" id="image" name="image" type="file" v-on:change="uploadfile">
                        </div>
                        <div v-if="errors.image" class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors.image }">{{errors.image?errors.image[0]:''}}
                        </div>
                    </div>

                    <div class="col-sm-12 form-group">
                        <label class="control-label hidden-xs full-width text-left">Comments/Special Instructions</label>
                        <textarea rows="1" v-model="formData.comments" class="test-comment" placeholder=""></textarea>
                    </div>
                </div>
                <div class="col-sm-12 no-padding separator"><hr width="100%" /></div>

                <!-- <div class="col-sm-12">
                    <div class="col-sm-12 form-group page-sub-title">ACCOUNTING</div>
                </div> -->
                <div class="col-sm-12 form-group" style="padding:0 30px;">
                    <div class="col-sm-12 no-padding element-border">
                        <div class="col-sm-6 no-padding">Subtotal</div>
                        <div class="col-sm-6 text-right text-bold no-padding" style="font-size:16px;">{{totalAmount}}</div>
                    </div>
                </div>

                <div class="col-sm-12">
                    <div class="col-md-2 form-group">
                        <div class="col-sm-12 element-border discount-label">
                            Discount
                        </div>
                    </div>
                    <div class="col-md-5 form-group">
                        <input v-bind:disabled="amount.discount_coupon!=''" v-on:change="applyDiscount" placeholder="%" class="md-input max-input-width text-right" type="text" v-model="amount.discount_percent">
                        <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors['discount_percent'] }">{{errors['discount_percent']?errors['discount_percent'][0]:''}}
                        </div>
                    </div>
                    <div class="col-md-5 form-group">
                        <input v-on:keyup="setDiscountManual" placeholder="0" class="md-input max-input-width text-right" type="text" v-model="amount.discount_amount">
                        <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['amount.discount_amount'] }">{{errors['amount.discount_amount']?errors['amount.discount_amount'][0]:''}}
                        </div>
                    </div>
                </div>

                <div class="col-sm-12 form-group" style="padding:0 30px;">
                    <input v-bind:disabled="amount.discount_percent!=''" placeholder="Enter Coupon" class="md-input input-with-icon" type="text" v-model="amount.discount_coupon">
                    <span class="right-input-btn" style="padding:0 30px;">
                        <button v-on:click="applyDiscount" v-bind:disabled="amount.discount_percent!=''" type="button" class="btn btn-primary btn-radius btn-shriked">APPLY COUPON</button>
                    </span>
                    <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors.discount_coupon }">{{errors.discount_coupon?errors.discount_coupon[0]:''}}
                    </div>
                </div>




                <!-- <div class="col-sm-12 form-group">
                    <div class="col-sm-3">Discount(%)</div>
                    <div class="col-sm-3">
                        <select class="form-control col-sm-12" v-model="amount.discount_type" id="discount_type">
                            <option value="">Select Promo Code</option>
                            <option value="promo_code">Promo Code</option>
                            <option value="percentage">Percentage</option>
                        </select>
                        <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['amount.discount_type'] }">{{errors['amount.discount_type']?errors['amount.discount_type'][0]:''}}
                        </div>
                    </div>
                    <div class="col-sm-3">
                        <input v-bind:disabled="!amount.discount_type" placeholder="Promo code" class="md-input max-input-width" type="text" id="discount_value" v-model="amount.discount_value" v-on:change="applyDiscount">
                        <div class="col-md-12 " v-bind:class="{ 'text-danger': errors['amount.discount_value'] }">{{errors['amount.discount_value']?errors['amount.discount_value'][0]:''}}
                        </div>
                    </div>
                    <div class="col-sm-3 text-right text-bold">
                        <input placeholder="Discount" class="md-input max-input-width text-right" type="text" v-model="amount.discount_amount" v-on:keyup="setDiscount" style="color:red;">
                    </div>
                </div> -->
                <!-- <div class="col-sm-12 form-group">
                    <div class="col-sm-6">Advance</div>
                    <div class="col-sm-3">
                        <select v-model="amount.advance_type" class="form-control col-sm-12" id="advance_type">
                            <option value="cash">Cash</option>
                            <option value="cheque">Cheque</option>
                        </select>
                    </div>
                    <div class="col-sm-3 text-right text-bold"><input placeholder="Amount" class="md-input max-input-width text-right" type="text" v-model="amount.advance_amount"></div>
                </div> -->

                <div class="col-sm-12 form-group subtotal-box gradient-box">
                    <div class="col-sm-6">Subtotal</div>
                    <div class="col-sm-6 text-right" style="font-weight: bold;">{{balanceAmount}}</div>
                </div>

                <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title page-sub-heading">ADVANCE</div></div>
                <div class="col-sm-12 form-group">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Select Method</label>
                        <select v-model="amount.advance_type" class="form-control col-sm-12" id="advance_type">
                            <option value="cash">Cash</option>
                            <option value="cheque">Cheque</option>
                        </select>
                        <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors.advance_type }">{{errors.advance_type?errors.advance_type[0]:''}}
                        </div>
                    </div>
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Amount</label>
                        <input placeholder="0" class="md-input max-input-width text-right" type="text" v-model="amount.advance_amount">
                        <div class="no-padding col-md-12 " v-bind:class="{ 'text-danger': errors.advance_amount }">{{errors.advance_amount?errors.advance_amount[0]:''}}
                        </div>
                    </div>
                </div>
                <div class="col-sm-12 form-group" style="padding:0 30px;">
                    <div class="col-sm-12 no-padding element-border">
                        <div class="col-sm-6 no-padding">Balance (Rs)</div>
                        <div class="col-sm-6 text-right text-bold no-padding" style="font-size:16px;"><strong>{{totalAmount}}</strong></div>
                    </div>
                </div>
                <div class="col-sm-12 form-group footer-buttons">
                    <div class="col-sm-3 col-sm-offset-2 form-group">
                        <button class="btn btn-default btn-block btn-lg btn-radius" type="button"><strong>Cancel</strong></button>
                    </div>
                    <div class="col-sm-3 form-group">
                        <button class="btn btn-primary btn-block btn-lg pon-btn" type="button">Save and Print</button>
                    </div>
                    <div class="col-sm-3 form-group">
                        <button class="btn btn-primary btn-block btn-lg pon-btn" type="submit">Save</button>
                    </div>
                </div>

            </div>
        </div>
        <search-patient></search-patient>
        <!-- Add Doctor Modal -->
        <div class="modal fade in" id="addDoctor" role="dialog" aria-labelledby="exampleModalLabel">
            <div class="col-sm-8 col-sm-offset-2 modal-dialog-30">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">Search Patient</h4>
                    </div>
                    <div class="modal-body">
                        <add-doctor></add-doctor>
                    </div>
                </div>
            </div>
        </div>
        <!-- Add Doctor Modal -->
        {{getQualifications}}
        <!-- {{ check_route_param }} -->
    </form>
</template>
<script>
    import searchPatient from '../reports/SearchPatient.vue';
    import addDoctor from '../doctors/AddDoctor.vue';
    export default {
        mounted() {
            if(this.$route.params.id != undefined){
                this.$parent.page_title = "Edit Registration Test";

            }else{
                this.$parent.page_title = "New Registration for Test";

            }
            this.applySelect2();
            this.applyDatePicker();
            this.setDefaultValues();
            this.getReportList();
            this.getDoctors();
            this.getCompany();
            //Wait to complete above 5 methods then call getReportMaster
            if(this.method_counter == 5){
                if(this.$route.params.id != undefined){
                    this.getReportMaster(this.$route.params.id);
                }else{
                    /*this.setTestNo();*/
                    this.setInvoiceDate();
                    this.showloader = false;
                }
            }
        },
        components: {
          'searchPatient': searchPatient,
          'addDoctor' : addDoctor
        },
        data: function() {
            return {
                isManual: false,
                users: [{username:"",password:"",role:""}],
                showloader: true,
                errors: {},
                error:{},
                formData:{ test_no:'',selfReference:false},
                patient:{gender:"", prefix:"", isDob:false, age_year:0,age_month:0,country_code:91 },
                to_print: false,
                doctor_id_list:[],
                doctor_id:{},
                company_id_list:[{id:1,name:"Company1",address:"123 dhantoli",branch_no:"1111"},{id:2,name:"Company2",address:"94 Sadar",branch_no:"321"}],
                company_id:{},
                test_list:[],
                test_selected: [{id:'',price:''}],
                amount:{
                    total:0,
                    discount_type:'',
                    discount_value:'',
                    discount_amount:"",
                    advance_type:'cash',
                    advance_amount:"",
                    other_charges:"",
                    discount_percent:"",
                    discount_coupon:"",
                },
                method_counter:0,
                qualifications: this.$parent.qualifications,
                doctorFormChanged: false,
                temp_image:false,
            }
        },
        computed:{
            getQualifications: function(){
                this.qualifications = this.$parent.qualifications;
            },
            totalAmount: function(){
                var total = 0;
                this.test_selected.map(function(e){total += +e.price;});
                this.amount.total = parseInt(total);
                return parseInt(total);
            },
            balanceAmount: function() {
                return this.amount.total + (+this.amount.other_charges) - (+this.amount.advance_amount) + (+this.amount.discount_amount) ;
            }/*,
            check_route_param: function(){
                if(this.$route.params.id === undefined){
                    this.resetForm();
                }
            }*/
        },
        methods: {
            resetForm: function(){
                console.log("resetForm");
                this.isManual = false;
                this.users = [{username:"",password:"",role:""}];
                this.showloader = true;
                this.errors = {};
                this.formData = {test_no:''};
                this.patient = {gender:"", prefix:"", isDob:false, age_year:0,age_month:0,country_code:91 };
                this.test_selected = [{id:'',price:''}];
                this.amount = {
                    total:0,
                    discount_type:'',
                    discount_value:'',
                    discount_amount:"",
                    advance_type:'cash',
                    advance_amount:"",
                    other_charges:"",
                    discount_percent:"",
                    discount_coupon:"",
                };
                this.method_counter=0;
                this.temp_image = false;

                this.applyDatePicker();
                this.setDefaultValues();
                this.getReportList();
                this.getDoctors();
                this.getCompany();
                if(this.method_counter == 5){
                    this.showloader = false;
                    var vm = this;
                    setTimeout(function(){
                        vm.applySelect2();
                        vm.setTestNo();
                        vm.setInvoiceDate();
                    },1);
                }
            },

            setDefaultValues: function(){
                $("#prefix").val("").change();
                $("#gender").val("").change();
                $("#country_code").val("91").change();
                $("#doctor_id").val("").change();
                $("#company_id").val("").change();
                $(".test-name").val("").change();
                this.method_counter++;
            },
            setForPrint: function() {
                this.to_print = true;
            },
            goHome: function() {
                location.href = '#/home';
            },
            submitForm: function(){
                console.log("submitForm");
                console.log(this.formData);
                console.log(this.patient);
                window.scrollTo(0,0);
                this.formData.patient_detail = "";
                this.formData.doctor_detail = "";
                this.formData.company_detail = "";
                this.formData.patient = this.patient;
                this.formData.test_detail = this.test_selected;
                this.formData.amount = this.amount;
                this.errors = {};
                this.showloader = true;
                this.$http.post('/report_master',this.formData).then((response) => {
                    if(response.body.isvalid){
                        if(response.body.success){
                            $('html,body').scrollTop(0);
                            this.error = {'type':'text-success' ,'message':'Updated successfully'};
                            this.showloader =  false;
                            setTimeout(function(){
                                location.href = '#/redirect';
                            },1000);
                        }else{
                            this.showloader =  false;
                            this.error = {'type':'text-danger' ,'message':response.body.message};
                        }
                    }else{
                        this.showloader =  false;
                        this.errors = response.body.errors;
                        console.log(this.errors);
                    }
                }, (response) => {
                    console.log(response);
                });
            },
            changeBoxToSingleLine: function(){
                $(".select2-container").addClass("form-select2 text-left").css('width','100%');
                $(".select2-selection").css("border","none");
                $(".select2-selection__rendered").css("padding","0px");
            },
            applySelect2: function(){
                var vm = this;
                $(".applySelect2").select2({placeholder: "Select Test"})
                .on("change", function(){
                    var value= $(this).val();
                    var id = $(this).attr('id');
                    vm.test_selected[id].id = value;
                    var index = vm.test_list.findIndex(test => test.id == value);
                    vm.test_selected[id].price = vm.test_list[index].fee;
                });
                $("#discount_type")
                .select2({ data: this.options })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.amount[id] = $(this).val();
                    vm.amount.discount_value = "";
                });
                $("#prefix,#country_code")
                .select2({ data: this.options })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.patient[id] = $(this).val();
                });
                $("#gender")
                .select2({ data: this.options, placeholder: "Select Gender" })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.patient[id] = $(this).val();
                });
                $("#advance_type")
                .select2({ data: this.options })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.formData[id] = $(this).val();
                });
                $("#doctor_id")
                .select2({ data: this.options,placeholder: "Referred by Doctor" })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.formData[id] = $(this).val();
                    vm.getDetails($(this));
                    if(id=="doctor_id" && $(this).val()){
                        vm.formData["selfReference"] = false;
                    }
                });
                $("#company_id")
                .select2({ data: this.options,placeholder: "Select Company" })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.formData[id] = $(this).val();
                    vm.getDetails($(this));
                    if(id=="doctor_id" && $(this).val()){
                        vm.formData["selfReference"] = false;
                    }
                });
                this.changeBoxToSingleLine();
                //this.method_counter++;
            },
            applyDatePicker: function() {
                $("#invoice_date").datepicker({ dateFormat: 'DD,M d yy',dayNames: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'] });
                var vm = this;
                $("#dob").datepicker({
                    dateFormat: 'DD,M d yy',maxDate:'0',
                    onSelect: function(){
                        var id = $(this).attr('id');
                        vm.patient[id] = $(this).val();
                        vm.setAge();
                     }
                });
                this.method_counter++;
            },
            setAge: function(){
                this.patient.dob = $("#dob").val();
                var today = new Date();
                var dob = new Date($("#dob").val());
                this.patient.age_year = 0;
                if(today.getFullYear() > dob.getFullYear()){
                    this.patient.age_month = today.getMonth() + 12 - dob.getMonth();
                }else{
                    this.patient.age_month = dob.getMonth() - today.getMonth();
                }
                if((today.getFullYear() - dob.getFullYear()) > 1 ){
                    this.patient.age_year = today.getFullYear() - dob.getFullYear() - 1;
                }
                if(this.patient.age_month > 11){
                    this.patient.age_month -= 12;
                    this.patient.age_year += 1;
                }
            },
            getDetails: function(elem){
                if(elem.val()){
                    var index = this[elem.attr('id')+"_list"].findIndex(x => x.id == elem.val())
                    this[elem.attr('id')] = this[elem.attr('id')+"_list"][index];
                }else{
                    this[elem.attr('id')] = {};
                }
            },
            addLine: function(){
                var index = Object.keys(this.test_selected).length;
                    this.test_selected.push({id:'',price:''});
                    var vm = this;
                  setTimeout(function(){
                    $("#"+index).select2({placeholder: "Select Test"})
                    .on("change", function(){
                        var value= $(this).val();
                        var id = $(this).attr('id');
                        vm.test_selected[id].id = value;
                        var index = vm.test_list.findIndex(test => test.id == value);
                        vm.test_selected[id].price = vm.test_list[index].fee;
                    });
                    $(".row_"+index+" .select2-container").addClass("form-select2 text-left").css('width','100%');
                    $(".row_"+index+" .select2-selection").css("border","none");
                    $(".row_"+index+" .select2-selection__rendered").css("padding","0px");
                  },1);
            },
            removeLine: function(index){
                $(".applySelect2").select2("destroy");
                this.test_selected.splice(index, 1);
                setTimeout(function(){
                    $(".applySelect2").select2({placeholder: "Select Test"});
                    $(".test-details .select2-container").addClass("form-select2 text-left").css('width','100%');
                    $(".test-details .select2-selection").css("border","none");
                    $(".test-details .select2-selection__rendered").css("padding","0px");
                },1);
            },
            setDiscount: function() {
                var vm = this;
                $("#discount_type")
                .select2({ data: this.options })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.amount[id] = $(this).val();
                    vm.amount.discount_value = "";
                });
                this.changeBoxToSingleLine();
                this.amount.discount_amount = -Math.abs(this.amount.discount_amount);
                $("#discount_type").val("").change();
            },
            applyDiscount: function() {
                //console.log(this.amount.discount_type);
                //console.log(this.amount.discount_value);
                if(this.amount.discount_coupon !=''){
                    if(this.amount.discount_coupon=='grab4' && this.amount.total){
                        this.amount.discount_amount = parseInt((this.amount.total*4)/100);
                        this.amount.discount_amount = -Math.abs(this.amount.discount_amount);
                    }
                    else if(this.amount.discount_coupon=='grab5' && this.amount.total){
                        this.amount.discount_amount = parseInt((this.amount.total*5)/100);
                        this.amount.discount_amount = -Math.abs(this.amount.discount_amount);
                    }else{
                        this.amount.discount_amount = "";
                    }
                }else{
                    var discount_percent = parseInt(+this.amount.discount_percent.replace("%",""));
                    if(discount_percent && this.amount.total){
                        this.amount.discount_percent = discount_percent + "%";
                        this.amount.discount_amount = parseInt((this.amount.total*discount_percent)/100);
                        this.amount.discount_amount = -Math.abs(this.amount.discount_amount);
                    }else{
                        this.amount.discount_percent = "";
                        this.amount.discount_amount = "";
                    }
                }
            },
            setManual: function() {
                var date = $("#invoice_date").val();
                this.isManual=!this.isManual;
                var vm = this;
                setTimeout(function(){
                    $("#invoice_date").datepicker("setDate", date);
                    vm.formData.date = $("#invoice_date").val();
                },1);
            },
            setTestNo: function() {
                //this.formData.test_no = parseInt(('0'+parseInt(Math.random() * 10000)).slice(-4));
                this.$http.get('/get_test_number').then((response) => {
                    if(response.body.success){
                        var test_number = response.body.test_number;
                        test_number = ('000000' + test_number).slice(-6);
                        this.formData.test_no = test_number;
                    }else{
                        console.log("error in getting detail");
                    }
                }, (response) => {
                    console.log(response);
                });
                //this.method_counter++;
            },
            checkTestNo: function() {
                this.$http.get('/check_test_number/'+this.formData.test_no).then((response) => {
                    if(response.body.report_exist){
                        this.errors = {test_no:["The test no has already been taken."]};
                    }
                }, (response) => {
                    console.log(response);
                });
            },
            getPatient: function() {

            },
            getReportList: function() {
                this.$http.get('/get_test_reports').then((response) => {
                    if(response.body.success){
                        this.test_list = response.body.reports;
                    }else{
                        console.log("error in getting detail");
                    }
                }, (response) => {
                    console.log(response);
                });
                this.method_counter++;
            },
            getDoctors: function() {
                this.$http.get('/doctors').then((response) => {
                    if(response.body.success){
                        this.doctor_id_list = response.body.doctors;
                    }else{
                        console.log("error in getting detail");
                    }
                }, (response) => {
                    console.log(response);
                });
                this.method_counter++;
            },
            getCompany: function() {
                this.$http.get('/get_all_company').then((response) => {
                    if(response.body.success){
                        this.company_id_list = response.body.company;
                    }else{
                        console.log("error in getting detail");
                    }
                }, (response) => {
                    console.log(response);
                });
                this.method_counter++;
            },
            setInvoiceDate: function(){
                var vm = this;
                setTimeout(function(){
                    $("#invoice_date").datepicker("setDate", new Date())
                    .on("change", function(){
                        vm.formData.invoice_date = $("#invoice_date").val();
                    });
                    vm.formData.invoice_date = $("#invoice_date").val();
                },1);
            },
            getReportMaster: function(report_id){
                this.showloader = true;
                this.$http.get('/report_master/'+report_id).then((response) => {
                    if(response.body.success){
                        this.formData = response.body.report;
                        this.patient = response.body.report.patient;
                        this.test_selected = this.formData.test_selected;
                        this.amount = this.formData.amount;
                        if(!this.formData.doctor_id){
                            this.formData.selfReference = true;
                        }
                        var vm = this;
                        setTimeout(function(){
                            vm.applySelect2();
                            //vm.setTestNo();
                            //vm.setInvoiceDate();
                            $("#company_id,#doctor_id").change();
                        },1000);
                        this.showloader = false;
                    }else{
                        console.log("error in getting detail");
                    }
                }, (response) => {
                    console.log(response);
                });
            },
            checkSelfRef: function(){
                if(this.formData.selfReference){
                    $("#doctor_id").val("").change();
                }
            },
            addDoctorCalled: function(){
                if(!this.doctorFormChanged){
                    this.doctorFormChanged = true;
                }else{
                    this.doctorFormChanged = false;
                    this.doctorFormChanged = true;
                }
            },
            changeInPercent: function(){
                var discount_percent = this.amount.discount_percent.replace("%","");
                if(discount_percent){
                    this.amount.discount_percent = discount_percent + "%";
                    this.amount.discount_amount = (this.amount.total*discount_percent)/100;
                }else{
                    this.amount.discount_percent = "";
                }
            },
            setDiscountManual: function(){
                this.amount.discount_percent='';
                this.amount.discount_coupon='';
                if(-(+this.amount.discount_amount)){
                    this.amount.discount_amount = -Math.abs(this.amount.discount_amount);
                }else{
                    this.amount.discount_amount = "";
                }
                /*if(this.amount.discount_amount){
                    this.amount.discount_amount = -this.amount.discount_amount;
                }else{
                    this.amount.discount_amount = "";
                }*/
            },
            uploadfile: function(){
                var imagefile = document.querySelector('#image');
                if (imagefile.files && imagefile.files[0]) {
                    this.temp_image = true;
                    var reader = new FileReader();
                    reader.onload = function (e) {
                        $('#temp_image')
                            .attr('src', e.target.result);
                    };
                    reader.readAsDataURL(imagefile.files[0]);
                    this.imagefile = imagefile.files[0];
                }else{
                    console.log("Image not selected");
                }
            },

        }
    }
</script>