<template>
    <form id="test_registration_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
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


                <div class="col-sm-12">
                    <div class="col-md-2 form-group text-center">
                        <img v-bind:src="temp_image" id="temp_image" style="border: 1px solid; width: 107px;height:107px;" class="img-circle">
                    </div>
                    <div class="col-md-10 form-group no-padding">
                        <div class="full-width col-sm-12" style="overflow: hidden; line-height: 100px;">
                        <label for="file-upload" style="font-weight: normal ! important; width: 100%; border: 1px dashed; padding-left: 10px;" class="custom-file-upload">
                            <i style="font-size: 3em; vertical-align: middle;" class="glyphicon glyphicon-file"></i> Drag & Drop or Browse
                            </label> <input class="full-width " value="" style="position: absolute; top: 0px; opacity: 0;" id="image" name="image" type="file" v-on:change="uploadfile">
                        </div>
                        <div v-if="errors.image" class="col-md-12" v-bind:class="{ 'text-danger': errors.image }">{{errors.image?errors.image[0]:''}}
                        </div>
                    </div>
                </div>

                <div class="col-sm-12">
                    <div class="col-md-2 form-group text-center">
                        <label class="control-label hidden-xs full-width text-left">Name</label>
                        <div class="pos-relative">
                            <select v-model="profile.user_prefix" id="user_prefix" name="user_prefix" class="form-control">
                                <option value="">Select Prefix</option>
                                <option value="dr">Dr.</option>
                                <option value="mr">Mr.</option>
                                <option value="mrs">Mrs.</option>
                            </select>
                            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.user_prefix }">{{errors.user_prefix?errors.user_prefix[0]:''}}
                            </div>
                        </div>
                    </div>
                    <div class="col-md-5 form-group text-center">
                        <label class="control-label hidden-xs">&nbsp;</label>
                        <input v-model="profile.first_name" placeholder="First Name" class="md-input " type="text">
                        <div class="col-md-12" v-bind:class="{ 'text-danger': errors.first_name }">{{errors.first_name?errors.first_name[0]:''}}
                        </div>
                    </div>
                    <div class="col-md-5 form-group text-center">
                        <label class="control-label hidden-xs">&nbsp;</label>
                        <input v-model="profile.last_name" placeholder="Last Name" class="md-input " type="text">
                        <div class="col-md-12" v-bind:class="{ 'text-danger': errors.last_name }">{{errors.last_name?errors.last_name[0]:''}}
                        </div>
                    </div>
                </div>

                <div class="col-sm-12">
                    <div class="col-md-6 form-group text-center">
                        <label class="control-label hidden-xs full-width  text-left">Gender</label>
                        <div>
                            <select v-model="profile.gender" id="gender" class="form-control">
                                <option value="">Select Gender</option>
                                <option value="male">Male</option>
                                <option value="female">Female</option>
                            </select>
                            <div class="col-md-12" v-bind:class="{ 'text-danger': errors.gender }">{{errors.gender?errors.gender[0]:''}}
                            </div>
                        </div>
                    </div>
                    <div class="col-md-6 form-group text-center">
                        <div class="flex-center">
                            <div class="full-width ">
                                <label class="control-label hidden-xs full-width  text-left">Date</label>
                                <div class="" style="position: relative;">
                                    <input  v-model="profile.dob" id="dob" placeholder="Date of birth" class="md-input " type="text">
                                    <span style="position: absolute;right: 0;top: 10px;">
                                        <i style="" class="glyphicon glyphicon-calendar"></i>
                                    </span>
                                    <div class="col-md-12" v-bind:class="{ 'text-danger': errors.dob }">{{errors.dob?errors.dob[0]:''}}
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>


                </div>

                <div class="col-sm-12">
                  <div class="col-md-2 form-group text-center">
                    <label class="control-label hidden-xs full-width  text-left">Mobile</label>
                    <div>
                        <select v-model="profile.country_code" id="country_code" class="form-control col-sm-4">
                          <option value="91">+91</option>
                          <option value="92">+92</option>
                          <option value="1">+1</option>
                        </select>
                        <div class="col-md-12" v-bind:class="{ 'text-danger': errors.country_code }">{{errors.country_code?errors.country_code[0]:''}}
                        </div>
                    </div>
                  </div>
                  <div class="col-md-4 form-group text-center">
                    <label class="control-label">&nbsp;</label>
                    <input v-model="profile.mobile_no" id="mobile_no" placeholder="Mobile no." class="md-input " type="text">
                    <div class="col-md-12" v-bind:class="{ 'text-danger': errors.mobile_no }">{{errors.mobile_no?errors.mobile_no[0]:''}}
                    </div>
                  </div>
                  <div class="col-md-6 form-group text-center">
                    <label class="control-label hidden-xs full-width  text-left">Email</label>
                    <div>
                        <input v-model="profile.email" id="email" placeholder="Email" class="md-input " type="email">
                        <div class="col-md-12" v-bind:class="{ 'text-danger': errors.email }">{{errors.email?errors.email[0]:''}}
                        </div>
                    </div>
                  </div>
                </div>

                <div class="col-sm-12 form-group text-center footer-buttons" style="margin-top: 50px;">
                    <div class="form-group">
                    <button v-on:click="goHome" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
                    <div class="form-group">
                    <button type="submit" class="btn btn-primary rounded-corner">Save</button></div>
                </div>
            </div>
        </div>{{getUserProfile}}
    </form>
</template>
<script>
    export default {
        mounted() {
            this.$parent.page_title = "My Account";
            this.$parent.settings_menu = true;
            this.applySelect2();
            this.applyDatePicker();
        },
        computed: {
            getUserProfile: function(){
                this.profile = this.$parent.profile;
                if(this.profile.image){
                    this.temp_image = "/images/profile/"+this.profile.image;
                }else{
                    this.temp_image = "/images/no_image_female.png";
                }
                var vm = this;
                setTimeout(function(){
                    vm.updateAllUnboundValue();
                },1);
            }
        },
        data: function() {
            return {
                showloader: false,
                errors: {},
                error:{},
                profile:this.$parent.profile,
                token: this.$parent.message,
                temp_image:"/images/no_image_female.png"
            }
        },
        methods: {
            goHome: function() {
                location.href = '#/home';
            },
            submitForm: function(){
                this.showloader = true;
                this.errors = {};
                this.setAllUnboundValue();
                var imagefile = document.querySelector('#image');
                var formData = new FormData();
                if (imagefile.files && imagefile.files[0]) {
                    formData.append("image", imagefile.files[0]);
                }
                for(var data in this.profile){
                    formData.append(data, this.profile[data]);
                }

                this.$http.post('/update_user_profile',formData).then((response) => {
                    this.showloader = false;
                    if(response.body.isvalid){
                        if(response.body.success){
                            console.log(response.body.image_name);
                            if(response.body.image_name){
                                this.$parent.profile.image = response.body.image_name;
                            }
                            this.error = {'type':'text-success','message':'Updated successfully.'};
                            //console.log("successfully updated");
                        }else{
                            this.error = {'type':'text-danger','message': response.body.message};
                            //console.log("error in update");
                        }
                    }else{
                        this.errors = response.body.errors;
                    }
                }, (response) => {
                    console.log(response);
                });
            },
            uploadfile: function(){
                var imagefile = document.querySelector('#image');
                if (imagefile.files && imagefile.files[0]) {
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
            setAllUnboundValue: function() {
                //this.profile.dob = $("#dob").val();
                //this.profile.user_prefix = $("#user_prefix").val();
                //this.profile.gender = $("#gender").val();
                //this.profile.country_code = $("#country_code").val();
            },
            updateAllUnboundValue: function() {
                //$("#dob").datepicker("setDate", new Date(this.profile.dob));
                $("#user_prefix").val(this.profile.user_prefix).change();
                $("#gender").val(this.profile.gender).change();
                $("#country_code").val(this.profile.country_code).change();
            },
            applySelect2: function(){
                var vm = this;
                $("#user_prefix,#gender,#country_code")
                .select2({ data: this.options })
                .on('change', function () {
                    var id = $(this).attr('id');
                    vm.profile[id] = $(this).val();
                    console.log($(this).val() + " selected");
                });
                $(".select2-container").addClass("form-select2 text-left").css('width','100%');
                $(".select2-selection").css("border","none");
                $(".select2-selection__rendered").css("padding","0px");
            },
            applyDatePicker: function() {
                var vm = this;
                $("#dob").datepicker({
                    dateFormat: 'd M yy',maxDate:'0',changeYear:true,
                    onSelect: function(){
                        console.log($(this).val());
                        vm.profile.dob = $(this).val();
                    }
                });
            }
        }
    }
</script>