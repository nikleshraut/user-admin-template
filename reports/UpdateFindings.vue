<template>
    <form id="test_registration_page" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
        <div class="max-form-width div-center form-center full-width">
            <div class="col-md-12 inner-body full-width no-padding">
                <div v-show="showloader" class="text-center loader">
                    <img src="/images/loading.gif">
                </div>

                <div v-show="error" class="col-sm-12 page-error">
                    <div v-bind:class="error.type" class="text-center">
                        {{error.message}}
                    </div>
                </div>
                <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title">PATIENT DETAILS </div></div>
                <div class="col-sm-12">
                    <div class="col-md-6 form-group no-padding">
                        <div class="col-md-3">Name :</div>
                        <div class="col-md-9">{{patient.first_name}} {{patient.last_name}}</div>
                    </div>
                    <div class="col-md-6 form-group no-padding">
                        <div class="col-md-3">Age/Sex :</div>
                        <div class="col-md-9">{{patient.age_year}} Year {{patient.age_month}}Months/{{patient.gender}}</div>
                    </div>
                </div>
                <template v-if="isNumerical">
                    <div class="col-sm-12">
                        <div class="col-md-4"><strong>Test Name</strong></div>
                        <div class="col-md-3"><strong>Reference Range</strong></div>
                        <div class="col-md-1"><strong>Unit</strong></div>
                        <div class="col-md-3"><strong>Finding</strong></div>
                    </div>
                    <hr style="border:1px solid;">
                    <template v-for="(test,index) in tests">
                        <template v-if="test.test === undefined">
                            <div class="col-sm-12" >
                                <strong>{{index}}</strong>
                            </div>
                            <template v-for="test in test" v-if="test.measurement_type !='Text' && test.measurement_type !='Options'">
                                <div class="col-sm-12">
                                    <div class="col-md-4">{{test.test}}</div>
                                    <div class="col-md-3">{{test.min_ref_range}} - {{test.max_ref_range}}</div>
                                    <div class="col-md-2">{{test.unit}}</div>
                                    <div class="col-md-3">
                                        <div class="pos-relative" v-bind:class="'finding_'+test.id">
                                            <input v-on:keyup="changeInputColor(test)" v-model="test.finding" placeholder="Finding" class="md-input input-with-icon" type="text">
                                            <i class="glyphicon glyphicon-edit"></i>
                                            <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.test.finding }">{{errors.test.finding?errors.test.finding[0]:''}}
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </template>
                        </template>
                        <template v-if="test.test !== undefined && (test.measurement_type !='Text' && test.measurement_type !='Options')">
                            <div class="col-sm-12" >
                                <div class="col-md-4">{{test.test}}</div>
                                <div v-if="test.ref_range_text" class="col-md-3">{{test.ref_range_text}}</div>
                                <div v-else class="col-md-3">{{test.min_ref_range}} - {{test.max_ref_range}}</div>
                                <div class="col-md-2">{{test.unit}}</div>
                                <div class="col-md-3">
                                    <div class="pos-relative" v-bind:class="'finding_'+test.id">
                                        <input v-model="test.finding" v-on:keyup="changeInputColor(test)" placeholder="Finding" class="md-input input-with-icon" type="text">
                                        <i class="glyphicon glyphicon-edit"></i>
                                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.test.finding }">{{errors.test.finding?errors.test.finding[0]:''}}
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </template>
                    </template>
                </template>
                <template v-if="isTextual">
                    <div class="col-sm-12">
                        <div class="col-md-4"><strong>Test Name</strong></div>
                        <div class="col-md-4"><strong>Text</strong></div>
                        <div class="col-md-4"><strong>Result</strong></div>
                    </div>
                    <hr style="border:1px solid;">
                    <template v-for="(test,index) in tests">
                        <template v-if="test.test !== undefined && (test.measurement_type =='Text' || test.measurement_type =='Options')">
                            <div class="col-sm-12" >
                                <div class="col-md-4">{{test.test}}</div>
                                <div v-if="test.ref_range_text" class="col-md-4">{{test.ref_range_text}}</div>
                                <div v-if="test.ref_range_options" class="col-md-4">{{test.ref_range_options}}</div>
                                <div class="col-md-4">
                                    <div class="pos-relative">
                                        <input v-model="test.finding" placeholder="Finding" class="md-input input-with-icon" type="text">
                                        <i class="glyphicon glyphicon-edit"></i>
                                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.test.finding }">{{errors.test.finding?errors.test.finding[0]:''}}
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </template>
                    </template>
                </template>

                <div class="col-sm-12 form-group text-center footer-buttons">
                    <div class="form-group">
                    <button v-on:click="cancelForm" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
                    <div class="form-group">
                    <div class="form-group">
                    <button type="submit" class="btn btn-primary rounded-corner">Save</button></div>
                </div>

            </div>
        </div>
    </form>
</template>
<script>
  export default {
    mounted() {
        console.log("Update Test Findings");
        this.$parent.page_title = "Update Test Findings";
        if(this.$route.params.id != undefined){
            this.updateFindings(this.$route.params.id);
        }
    },
    data: function() {
        return {
            showloader: false,
            errors: {test:{}},
            error:{},
            tests:[],
            patient:{},
            isTextual:false,
            isNumerical:false
        }
    },
    methods: {
        updateFindings: function(id){
            console.log("updateFindings : "+id);
            this.showloader =  true;
            this.$http.get('/update_findings/'+id).then((response) => {
                this.showloader =  false;
                this.tests = response.body.report.tests;
                console.log(this.tests);
                this.patient = response.body.report.patient;
                for (var key in this.tests) {
                  if(this.tests[key].test == undefined){
                    for (var key2 in this.tests[key]) {
                        if(this.tests[key][key2].measurement_type=="Text" || this.tests[key][key2].measurement_type=="Options"){
                            this.isTextual = true;
                        }else{
                            this.isNumerical = true;
                        }
                        this.tests[key][key2].red_border = false;
                        this.tests[key][key2].yellow_border = false;
                        this.tests[key][key2].green_border = false;
                    }
                  }else{
                    if(this.tests[key].measurement_type=="Text" || this.tests[key].measurement_type=="Options"){
                        this.isTextual = true;
                    }else{
                        this.isNumerical = true;
                    }
                    this.tests[key].red_border = false;
                    this.tests[key].yellow_border = false;
                    this.tests[key].green_border = false;
                  }
                }
            }, (response) => {
                console.log(response);
            });
        },
        submitForm: function(){
            this.showloader =  true;
            console.log("submitForm");
            console.log(this.tests);
            var formdata = {};
            formdata.tests = this.tests;
            this.$http.post('/update_finding',formdata).then((response) => {
                console.log(response.body);
                this.showloader =  false;
                this.error = {'type':'text-success' ,'message':'Updated successfully'};
                setTimeout(function(){
                    location.href = '#/add-findings';
                },1000);
            }, (response) => {
                console.log(response);
            });
        },
        cancelForm: function(){
            location.href = '#/add-findings';
        },
        changeInputColor:_.debounce(function (test) {
            $(".finding_"+test.id).removeClass("bg-danger");
            $(".finding_"+test.id).removeClass("bg-warning");
            $(".finding_"+test.id).removeClass("bg-success");
            if(test.finding){
                var diff = test.max_ref_range - test.min_ref_range;
                var percent = ((test.finding-test.min_ref_range)*100)/diff;
                console.log("% = "+percent,test.finding,test.min_ref_range,test.max_ref_range);
                if(test.finding < parseFloat(test.min_ref_range) || test.finding > parseFloat(test.max_ref_range)){
                    $(".finding_"+test.id).addClass("bg-danger");
                    console.log("red");
                }else if(percent<=5 || percent>=95){
                    $(".finding_"+test.id).addClass("bg-warning");
                    console.log("yellow");
                }else{
                    $(".finding_"+test.id).addClass("bg-success");
                    console.log("green");
                }
            }
        }, 500)
    }
  }
</script>