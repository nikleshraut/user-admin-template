<template>
    <form id="test_registration_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
        <div class="max-form-width div-center form-center full-width">
            <div class="col-md-10 inner-body full-width no-padding">

                <div v-show="error" class="col-sm-12 page-error">
                    <div v-bind:class="error.type" class="text-center">
                        {{error.message}}
                    </div>
                </div>

                <div class="col-sm-12">
                     <input type="checkbox" id="chk_customer" value="customer" v-model="apptype.is_customer"  />
                    <label for="chk_customer"><span style="font-size:18px;">Consumer App</span></label>
                </div>

                <div class="col-sm-12">
                    <p class="apptype-ptext">Loren ipsum dolar sit amet, Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet</p>
                </div>

                <div class="col-sm-12">
                    <input type="checkbox" id="chk_lab_owner" value="lab_owner" v-model="apptype.is_lab_owner"/>
                    <label for="chk_lab_owner"><span style="font-size:18px;">Pathology Labortory App</span></label>
                </div>

                <div class="col-sm-12">
                    <p class="apptype-ptext">Loren ipsum dolar sit amet, Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet</p>
                </div>

                <div class="col-sm-12">
                      <input type="checkbox" id="chk_ref_doctor" value="ref_doctor" v-model="apptype.is_ref_doctor" />
                    <label for="chk_ref_doctor"><span style="font-size:18px;">Doctor App</span></label>
                </div>

                <div class="col-sm-12">
                    <p class="apptype-ptext">Loren ipsum dolar sit amet, Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet Loren ipsum dolar sit amet</p>
                </div>
                
                <div class="col-sm-12 form-group text-center footer-buttons" style="margin-top: 50px;">
                    <div class="form-group">
                    <button v-on:click="goHome" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
                    <div class="form-group">
                    <button type="submit" class="btn btn-primary rounded-corner">Save</button></div>
                </div>
            </div>{{getAppType}}
        </div>
    </form>
</template>
<script type="text/javascript">
    export default {
        mounted() {
            console.log("App Type");
            this.$parent.page_title = "App Type";
        },
        computed: {
            getAppType: function(){
                var user_app_type = this.$parent.profile;

                this.apptype = {is_customer:user_app_type.is_customer, is_ref_doctor:user_app_type.is_ref_doctor, is_lab_owner:user_app_type.is_lab_owner};

            }
        },
        data: function() {
            return {
                errors: {},
                error:{},
                apptype:{is_customer:false, is_ref_doctor:false, is_lab_owner:false}
            }
        },
        methods: {
            goHome: function() {
                location.href = '#/home';
            },
            submitForm: function(){
                var formData = this.apptype;
                this.$http.post('/update_app_type',formData).then((response) => {
                    this.showloader = false;

                    if(response.body.isvalid){
                        if(response.body.success){
                            /*if(response.body.image_name){
                                this.$parent.profile.image = response.body.image_name;
                            }*/
                            this.error = {'type':'text-success','message':'Updated successfully.'};
                            //console.log("successfully updated");
                        }else{
                            this.error = {'type':'text-danger','message': response.body.message.apptype[0]};
                            //console.log("error in update");
                        }
                    }else{
                        this.error = {'type':'text-danger','message': response.body.message.apptype[0]};
                    }
                }, (response) => {
                    console.log(response);
                });
            }
        }
    }
</script>