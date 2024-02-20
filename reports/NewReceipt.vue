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

                <!-- new row -->
                <div class="col-sm-12">
                    <div class="col-md-6 form-group">
                        <label class="control-label hidden-xs full-width text-left">Date</label>
                        <div class="pos-relative">
                            <input v-model="formData.date" id="date" placeholder="Date" class="md-input input-with-icon" type="text">
                            <i class="glyphicon glyphicon-calendar"></i>
                            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.date }">{{errors.date?errors.date[0]:''}}
                            </div>
                        </div>
                    </div>
                    <div class="col-md-6 form-group lab_name-form-group">
                        <label class="control-label hidden-xs full-width text-left">Name</label>
                        <div class="pos-relative">
                            <select v-model="formData.user_prefix" id="user_prefix" name="user_prefix" class="form-control">
                                <option value="">Select Prefix</option>
                                <option value="dr">Dr.</option>
                                <option value="mr">Mr.</option>
                                <option value="mrs">Mrs.</option>
                            </select>
                            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.user_prefix }">{{errors.user_prefix?errors.user_prefix[0]:''}}
                            </div>
                        </div>
                    </div>
                </div>
                <!-- row end -->


            </div>
        </div>
    </form>
</template>
<script>
  export default {
    mounted() {
        console.log("New Receipt");
        this.$parent.page_title = "New Test Receipt";
        this.setDefaultValues();
        this.applySelect2();
    },
    data: function() {
        return {
            showloader: false,
            errors: {},
            error:{},
            formData:{}
        }
    },
    methods: {
        setDefaultValues: function(){
            $("#user_prefix").val("").change();
            $("#gender").val("").change();
        },
        setForPrint: function() {
            console.log("setForPrint");
        },
        goHome: function() {
            location.href = '#/home';
        },
        submitForm: function(){
            console.log("submitForm");
        },
        setAllUnboundValue: function() {
        },
        updateAllUnboundValue: function() {
        },
        applySelect2: function(){
            $("#user_prefix")
            .select2({ data: this.options })
            .on('change', function () {
                console.log($(this).val() + " selected");
            });
            $(".select2-container").addClass("form-select2 text-left").css('width','100%').css('max-width','400px');
            $(".select2-selection").css("border","none");
            $(".select2-selection__rendered").css("padding","0px");
        },
        getUserProfile: function() {
        }
    }
  }
</script>