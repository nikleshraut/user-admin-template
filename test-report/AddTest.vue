<template>
<div>
    <form  v-if="showPage" id="main_form" class="form-body full-height" style="overflow: auto;" role="form" method="POST" v-on:submit.prevent="submitForm">
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
                <div class="col-sm-12"><div class="col-sm-12 form-group page-sub-title">TEST DETAILS </div></div>
                <!-- new row -->
                <div class="col-sm-12">
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Name<span class="required-field">*</span></label>
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
                <div class="col-sm-12">
                  <div class="col-md-8 form-group">
                    <label class="control-label hidden-xs full-width text-left">Test Method<span class="required-field">*</span></label>
                    <div class="col-md-3">NABL : <input name="test_method" id="methodNABL" value="NABL" type="radio" v-model="formData.test_method" ></div>
                    <div class="col-md-3">Other : <input name="test_method" id="methodOther" value="Other" type="radio" v-model="formData.test_method"></div>
                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.test_method }">{{errors.test_method?errors.test_method[0]:''}}
                    </div>
                  </div>
                </div>
                <!-- row end -->


                <!-- new row -->
                <div class="col-sm-12">
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Test Group<span class="required-field">*</span></label>

                    <div class="col-md-2">BIOCHEM : <input name="test_group" id="groupBIOCHEM" value="BIOCHEM" type="radio" v-model="formData.test_group" v-on:change="getMeasurment"></div>

                    <div class="col-md-2">Hematology : <input name="test_group" id="groupHematology" value="Hematology" type="radio" v-model="formData.test_group" v-on:change="getMeasurment"></div>

                    <div class="col-md-2">ECI : <input name="test_group" id="groupECI" value="ECI" type="radio" v-model="formData.test_group" v-on:change="getMeasurment"></div>

                    <div class="col-md-2">Rochi : <input name="test_group" id="groupRochi" value="Rochi" type="radio" v-model="formData.test_group" v-on:change="getMeasurment"></div>

                    <div class="col-md-3">Clinical Path : <input name="test_group" id="groupClinicalPath" value="Clinical Path" type="radio" v-model="formData.test_group" v-on:change="getMeasurment"></div>

                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.test_group }">{{errors.test_group?errors.test_group[0]:''}}
                    </div>
                  </div>
                </div>
                <!-- row end -->

                <!-- new row -->
                <div class="col-sm-12">
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Measurement<span class="required-field">*</span></label>

                    <div class="col-md-2">U/M  : <input name="measurement_type" id="groupUM" value="U_M" type="radio" v-model="formData.measurement_type" v-on:change="getMeasurment"></div>

                    <div class="col-md-2">Boolean : <input name="measurement_type" id="groupBoolean" value="Boolean" type="radio" v-model="formData.measurement_type" v-on:change="getMeasurment"></div>

                    <div class="col-md-2">Options : <input name="measurement_type" id="groupOptions" value="Options" type="radio" v-model="formData.measurement_type" v-on:change="getMeasurment"></div>
                    
                    <!-- Reference Range Text -->
                    <div class="col-md-2">Text : <input name="measurement_type" id="groupText" value="Text" type="radio" v-model="formData.measurement_type" v-on:change="getMeasurment"></div>

                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.measurement_type }">{{errors.measurement_type?errors.measurement_type[0]:''}}
                    </div>
                  </div>
                </div>
                <!-- row end -->

                 <!-- new row -->
                <div class="col-sm-12" v-if="unit_row" >
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Unit</label>
                    <div class="pos-relative">
                        <select v-model="formData.unit_id" id="unit_id" name="unit_id" class="form-control">
                            <option value="">Select Unit</option>
                            <option v-for="unit in units" v-bind:value="unit.id">{{unit.name}}</option>
                        </select>
                        <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.unit_id }">{{errors.unit_id?errors.unit_id[0]:''}}
                        </div>
                    </div>
                  </div>
                </div>
                <!-- row end -->

                <!-- new row -->
                <div class="col-sm-12" v-if="max_min">
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Min/Max</label>
                    <div class="col-md-3">
                      <input v-model="formData.min_ref_range" placeholder="Min Range" class="md-input input-with-icon" type="text">
                    </div>
                    <div class="col-md-3">
                      <input v-model="formData.max_ref_range" placeholder="Max Range" class="md-input input-with-icon" type="text">
                    </div>
                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.min_ref_range }">{{errors.min_ref_range?errors.min_ref_range[0]:''}}

                    </div>
                  </div>
                </div>
                <!-- row end -->

                <!-- new row -->
                <div class="col-sm-12"  v-if="boolean_val">
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">True/False</label>

                    <div class="col-md-2">True  : <input name="ref_range_boolean" id="booleanVal" value="1" type="radio" v-model="formData.ref_range_boolean" ></div>

                    <div class="col-md-2">False : <input name="ref_range_boolean" id="booleanVal" value="0" type="radio" v-model="formData.ref_range_boolean"></div>

                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.measurement_type }">{{errors.measurement_type?errors.measurement_type[0]:''}}
                    </div>
                  </div>
                </div>
                <!-- row end -->

                <!-- new row -->
                <div class="col-sm-12" v-if="ref_options">
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Options</label>
                    <input v-model="formData.ref_range_options" placeholder="Options" class="md-input input-with-icon" type="text"><span class="small">Note: Please add option with comma seperated (eg. Detected, Not detected).</span>
                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.ref_range_options }">{{errors.ref_range_options?errors.ref_range_options[0]:''}}
                    </div>
                  </div>
                </div>
                <!-- row end -->

                <!-- Reference Range Text -->
                <!-- new row -->
                <div class="col-sm-12" v-if="ref_text">
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Text</label>
                    <input v-model="formData.ref_range_text" placeholder="Text" class="md-input input-with-icon" type="text"><span class="small">Note: Please add a text.</span>
                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.ref_range_text }">{{errors.ref_range_text?errors.ref_range_text[0]:''}}
                    </div>
                  </div>
                </div>
                <!-- row end -->

                <!-- Multiple Reference Range Start -->
                <div  v-if="multiple_ref_range">

                  <!-- Male Rows Start -->
                  <div class="row" >
                    <div class="col-md-4">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Reference Range for Male</label>
                    </div>
                  </div>
                  <!-- new row -->
                  <div class="col-sm-12 text-center">
                    <div class="col-md-3">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Minimum Age</label>
                    </div>
                    <div class="col-md-3">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Maximum Age</label>
                    </div>
                    <div class="col-md-2">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Min Range</label>
                    </div>
                    <div class="col-md-2">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Max Range</label>
                    </div>
                  </div>
                  <!-- row end -->

                  <div class="row" v-for="(row,index) in male_rows">
                    <div class="col-sm-12 text-center">

                      <div class="col-md-1">

                        <input v-model="row.male_record"  class="md-input input-with-icon" type="hidden">
                        <input v-model="row.male_min_days" placeholder="D" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-1">
                        <input v-model="row.male_min_months" placeholder="M" class="md-input input-with-icon" type="text">
                      </div>

                      <div class="col-md-1">
                        <input v-model="row.male_min_years" placeholder="Y" class="md-input input-with-icon" type="text">
                      </div>

                      <div class="col-md-1">
                        <input v-model="row.male_max_days" placeholder="D" class="md-input input-with-icon" type="text">
                      </div>

                      <div class="col-md-1">
                        <input v-model="row.male_max_months" placeholder="M" class="md-input input-with-icon" type="text">
                      </div>

                      <div class="col-md-1">
                        <input v-model="row.male_max_years" placeholder="Y" class="md-input input-with-icon" type="text">
                      </div>

                      <div class="col-md-2">
                        <input v-model="row.male_min_ref_range" placeholder="Min Range" class="md-input input-with-icon" type="text">
                      </div>

                      <div class="col-md-2">
                        <input v-model="row.male_max_ref_range" placeholder="Max Range" class="md-input input-with-icon" type="text">
                      </div>

                      <div class="col-md-2 form-group main-form-group">

                        <button type="button" v-if="index!='0'" v-on:click="removeMaleRow(index)"  style="padding: 0 10px;" class="btn btn-danger">
                          <i class="glyphicon glyphicon-trash"></i>
                        </button>
                        <button  type="button" v-on:click="addMaleRow" style="padding: 0 10px;" class="btn btn-primary">
                          <i class="glyphicon glyphicon-plus"></i>
                        </button>

                      </div>
                      <div class="col-sm-12 text-danger text-left">{{ errors["male_rows."+index] ? errors["male_rows."+index][0] : ""}}</div>


                    </div>
                    

                  </div>
                  <div class="row col-md-12 text-center  " v-html="errors.male_rows"  v-bind:class="{ 'text-danger': errors.male_rows }">{{errors.male_rows?errors.male_rows:''}}
                    </div>

                  <!-- Male Rows End -->

                  <!-- Female Rows Start -->
                  <div class="row" >
                    <div class="col-md-4">
                    <label class="control-label hidden-xs full-width text-left col-md-3">Reference Range for Female</label>
                    </div>
                  </div>
                  <!-- new row -->
                  <div class="col-sm-12 text-center">
                    <div class="col-md-3">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Minimum Age</label>
                    </div>
                    <div class="col-md-3">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Maximum Age</label>
                    </div>
                    <div class="col-md-2">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Min Range</label>
                    </div>
                    <div class="col-md-2">
                      <label class="control-label hidden-xs full-width text-left col-md-3">Max Range</label>
                    </div>
                  </div>
                  
                  <!-- row end -->
                  <div class="row" v-for="(female_row,index) in female_rows">
                    <div class="col-sm-12 text-center">
                      <div class="col-md-1">
                        <input v-model="female_row.female_record"  class="md-input input-with-icon" type="hidden">
                        <input v-model="female_row.female_min_days" placeholder="D" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-1">
                        <input v-model="female_row.female_min_months" placeholder="M" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-1">
                        <input v-model="female_row.female_min_years" placeholder="Y" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-1">
                        <input v-model="female_row.female_max_days" placeholder="D" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-1">
                        <input v-model="female_row.female_max_months" placeholder="M" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-1">
                        <input v-model="female_row.female_max_years" placeholder="Y" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-2">
                        <input v-model="female_row.female_min_ref_range" placeholder="Min Range" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-2">
                        <input v-model="female_row.female_max_ref_range" placeholder="Max Range" class="md-input input-with-icon" type="text">
                      </div>
                      <div class="col-md-2 form-group main-form-group">
                        <button type="button" v-if="index!='0'" v-on:click="removeFemaleRow(index)"  style="padding: 0 10px;" class="btn btn-danger">
                          <i class="glyphicon glyphicon-trash"></i>
                        </button>
                        <button  type="button" v-on:click="addFemaleRow" style="padding: 0 10px;" class="btn btn-primary">
                          <i class="glyphicon glyphicon-plus"></i>
                        </button>
                      </div>
                      <div class="col-sm-12 text-danger text-left">{{ errors["female_rows."+index] ? errors["female_rows."+index][0] : ""}}</div>

                    </div>
                  </div>

                  <div class="row col-md-12 text-center  " v-html="errors.female_rows"  v-bind:class="{ 'text-danger': errors.female_rows }">{{errors.female_rows?errors.female_rows:''}}
                    </div>

                  <!-- Female Rows End -->

                </div>
                <!-- Multiple Reference Range End -->

              <!-- new row -->
                <div class="col-sm-12"  >
                  <div class="col-md-12 form-group">
                    <label class="control-label hidden-xs full-width text-left">Status<span class="required-field">*</span></label>

                    <div class="col-md-2">Active  : <input name="status" id="status_yes" value="1" type="radio" v-model="formData.status" ></div>

                    <div class="col-md-2">Inactive : <input name="status" id="status_no" value="0" type="radio" v-model="formData.status"></div>

                    <div class="col-md-12 text-center " v-bind:class="{ 'text-danger': errors.status }">{{errors.status?errors.status[0]:''}}
                    </div>
                  </div>
                </div>
                <!-- row end -->

                <div class="col-sm-12 form-group text-center footer-buttons">
                    <div class="form-group">
                    <button v-on:click="AllTests" type="button" class="btn btn-default rounded-corner">Cancel</button></div>
                    <div class="form-group">
                    <button type="submit" class="btn btn-primary rounded-corner">Save</button></div>
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

        /*this.$parent.page_title = "Add/Edit Test Info";
        if(this.$route.params.id != undefined){
          this.updateRecord(this.$route.params.id);
        }
        //this.updateRecord(this.$route.params.id);
        this.applySelect2();
        this.getUnits();*/
    },
    data: function() {
        return {
            showloader: false,
            unit_row:false,
            max_min:false,
            boolean_val:false,
            ref_options:false,
            multiple_ref_range:false,
            ref_text:false,
            errors: {},
            error:{},
            formData:{unit_id:'',male_min_days:''},
            units:{},
            ref_ranges: [{min_day:"",min_month:"",min_year:"",min_refrange:"",max_day:"",max_month:"",max_year:"",max_refrange:""}],
            reference_range:{},

            male_rows: [],
            female_rows: [],
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
          if(this.formData.male_rows != undefined){
            this.formData.male_rows=this.male_rows;
          }else{
            this.formData.male_rows=""; 
          }
          
          if(this.formData.female_rows != undefined){
            this.formData.female_rows=this.female_rows;
          }else{
            this.formData.female_rows="";
          }          


            this.errors = {};
            this.showloader = true;
            //this.formData = {ref_ranges:this.ref_ranges};
            
           //alert(this.formData.male_rows); 
            
            this.$http.post('/tests',this.formData).then((response) => {
              /*console.log(response.body);
              alert("Done" + response.body.isvalid);
              alert("Done" + response.body.success);
              */

                //this.showloader = false;
                if(response.body.isvalid){
                    if(response.body.success){
                        this.error = {'type':'text-success' ,'message':'Updated successfully'};
                        this.$parent.tests = {};
                        this.showloader =  false;

                        this.getRecords();
                    }else{
                        this.showloader =  false;
                        this.error = {'type':'text-danger' ,'message':response.body.message};
                    }
                }else{
                    this.showloader =  false;
                    this.errors = response.body.errors;
                    //var errors = {};
                    //alert("this.errors :0: "+this.errors[0]);
                    //return false;                    
                    var maleErr="";
                    var femaleErr="";
                    for(var error in this.errors){
                      var arrerror = error.split(".");
                      
                      if(arrerror[0]=="male_rows"){
                        //this.errors["male_rows"]=this.errors[error];
                        if(arrerror[1]==0){
                          maleErr=maleErr+this.errors[error]+"<br>";  
                        }
                        
                        //alert(arrerror[0]+" :: "+arrerror[1]+" :: "+arrerror[2]+" :*: "+this.errors[error]+" :: "+maleErr);
                        //errors[arrerror[0]+"."+arrerror[1]] = this.errors[error];
                      }
                      if(arrerror[0]=="female_rows"){
                        if(arrerror[1]==0){
                          femaleErr=femaleErr+this.errors[error]+"<br>";  
                        }
                      }

                      //male_rows :: 0 :: male_min_days

                      /*if(errors[arrerror[1]] == undefined){
                        errors[arrerror[0]+"."+arrerror[1]] = this.errors[error];
                      }*/
                    }
                    if(maleErr!=""){
                      this.errors["male_rows"]=maleErr;
                    }
                    if(femaleErr!=""){
                      this.errors["female_rows"]=femaleErr;
                    }                    

                    /*console.log(this.errors);
                    var errors = {};
                    for(var error in this.errors){
                      var arrerror = error.split(".");
                      console.log(arrerror[0]+"."+arrerror[1]);
                      if(errors[arrerror[1]] == undefined){
                        errors[arrerror[0]+"."+arrerror[1]] = this.errors[error];
                      }
                    }
                    this.errors = errors;*/
                }
            }, (response) => {
                this.showloader =  false;
                console.log(response);
                 
            });

            
        },

        setAllUnboundValue: function() {
        },

        updateAllUnboundValue: function() {
        },

        applySelect2: function(){
            var vm = this;
            $("#unit_id")
            .select2({ data: this.options })
            .on('change', function () {
                console.log($(this).val() + " selected");
                vm.formData.unit_id = $(this).val();
            });
            $(".select2-container").addClass("form-select2 text-left").css('width','100%').css('max-width','400px');
            $(".select2-selection").css("border","none");
            $(".select2-selection__rendered").css("padding","0px");
        },

        updateRecord: function(test_id) {
          this.showloader =  true;
          this.$http.get('/tests/'+test_id).then((response) => {
            this.showloader =  false;
            this.formData = response.body.record;
            if(this.formData.is_multiple==0){
              this.reference_range= response.body.reference_range;
              console.log(this.reference_range);
              /*alert(this.reference_range.length.min_ref_range+" :: "+this.reference_range.length.max_ref_range);
              this.formData.min_ref_range=this.reference_range.min_ref_range;
              this.formData.max_ref_range=this.reference_range.max_ref_range;
*/
                

                var y={};
                for(var i = 0; i < this.reference_range.length; i++){

                  y=this.reference_range[i];
                  this.formData.min_ref_range=y.min_ref_range;
                  this.formData.max_ref_range=y.max_ref_range;                  
                  
                }              
            }else{
              this.reference_range= response.body.reference_range;
            }


            var update_test_group=this.formData.test_group;
            var updated_measurment=this.formData.measurement_type;
            this.unit_row=false;
            this.max_min=false;
            this.boolean_val=false;
            this.ref_options=false;
            this.multiple_ref_range=false;
            // <!-- Reference Range Text -->
            this.ref_text=false;

            // For Grop Hematology - Rochi - Clinical Path
            if(update_test_group=='Hematology' || update_test_group=='Rochi'  || update_test_group=="Clinical Path")
            {

              // If measurement type in unit measure
              if(updated_measurment=='U_M'){
                this.unit_row=true;
                this.max_min=true;
              }

            }

            // For Grop BIOCHEM - ECI
            if(update_test_group=='BIOCHEM' || update_test_group=='ECI')
            {
              // If measurement type in unit measure
              if(updated_measurment=='U_M'){
                //$( "#unit_row,#multiple_range_for_male,#multiple_range_for_male_max_min" ).removeClass( "hide" ).addClass( "show" );
                this.unit_row=true;
                this.multiple_ref_range=true;


                var x={};
                for(var i = 0; i < this.reference_range.length; i++){
                  x=this.reference_range[i];

                  // Male Records
                  if(x.gender=="Male"){
                    this.male_rows.push({male_record:"",male_min_days:x.min_days,male_min_months:x.min_months,male_min_years:x.min_years,male_max_days:x.max_days,male_max_months:x.max_months,male_max_years:x.max_years,male_min_ref_range:x.min_ref_range,male_max_ref_range:x.max_ref_range });
                  }

                  // Female Records
                  if(x.gender=="Female"){
                    this.female_rows.push({female_record:"",female_min_days:x.min_days,female_min_months:x.min_months,female_min_years:x.min_years,female_max_days:x.max_days,female_max_months:x.max_months,female_max_years:x.max_years,female_min_ref_range:x.min_ref_range,female_max_ref_range:x.max_ref_range });
                  }



                }



                //this.addMaleRow();
                //$("#multiple_ref_range,#unit_row").removeClass("hide").addClass("show");
              }
            }


            // For all groups
            if(update_test_group!='')
            {
              // If test value is in true-false
              if(updated_measurment=='Boolean'){
                //$( "#boolean_val" ).removeClass( "hide" ).addClass( "show" );
                this.boolean_val=true;
                this.formData.ref_range_boolean=this.reference_range.ref_range_boolean;
              }
              // If test value is in options
              if(updated_measurment=='Options'){
                this.ref_options=true;
                this.formData.ref_range_options=this.reference_range.ref_range_options;
              }

              // <!-- Reference Range Text -->
              // If test value is in text
              if(updated_measurment=='Text'){
                this.ref_text=true;
                this.formData.ref_range_text=this.reference_range.ref_range_text;
              }

            }


                /*var vm = this;
                setTimeout(function() {
                    vm.applySelect2();
                }, 1);*/
            }, (response) => {
                console.log(response);
            });
        },

        AllTests: function() {
            location.href = '#/tests';
        },

        getRecords: function() {
            this.$http.get('/tests').then((response) => {
                this.showloader =  false;
                this.formData = {};
                this.$parent.tests = response.body.records;
                location.href = '#/tests';
            }, (response) => {
                console.log(response);
            });
       },

      getUnits: function(){
        this.$http.get('/api/unit_group').then((response) => {
          this.units = response.body.units;
        }, (response) => {
          console.log(response);
        });

      },


      getMeasurment: function(){
        var checked_test_group=this.formData.test_group;
        var measurment=this.formData.measurement_type;
        this.unit_row=false;
        this.max_min=false;
        this.boolean_val=false;
        this.ref_options=false;
        this.multiple_ref_range=false;
        // <!-- Reference Range Text -->
        this.ref_text=false;


        //alert(checked_test_group+" :: "+measurment);

        // For single reference range
        ////$("#max_min,#boolean_val,#ref_options, #unit_row").removeClass("show").addClass("hide");

        // For multiple reference range
        //$("#multiple_range_for_male,#multiple_range_for_male_max_min, #unit_row").removeClass("show").addClass("hide");
        ////$("#multiple_ref_range,#unit_row").removeClass("show").addClass("hide");

        // For Grop Hematology - Rochi - Clinical Path
        if(checked_test_group=='Hematology' || checked_test_group=='Rochi'  || checked_test_group=="Clinical Path")
        {

          // If measurement type in unit measure
          if(measurment=='U_M'){
            this.unit_row=true;
            this.max_min=true;
            //$( "#unit_row,#max_min" ).removeClass( "hide" ).addClass( "show" );
          }
        }


        // For Grop BIOCHEM - ECI
        if(checked_test_group=='BIOCHEM' || checked_test_group=='ECI')
        {
          // If measurement type in unit measure
          if(measurment=='U_M'){
            //$( "#unit_row,#multiple_range_for_male,#multiple_range_for_male_max_min" ).removeClass( "hide" ).addClass( "show" );
            this.unit_row=true;
            this.multiple_ref_range=true;
            this.addMaleRow();
            this.addFemaleRow();
            //$("#multiple_ref_range,#unit_row").removeClass("hide").addClass("show");
          }
        }


      // For all groups
      if(checked_test_group!='')
      {
        // If test value is in true-false
        if(measurment=='Boolean'){
          //$( "#boolean_val" ).removeClass( "hide" ).addClass( "show" );
          this.boolean_val=true;
        }

        // If test value is in options
        if(measurment=='Options'){
          //$("#ref_options").addClass("show");
          this.ref_options=true;
        }

        // <!-- Reference Range Text -->
        // If test value is in text
        if(measurment=='Text'){
          this.ref_text=true;
        }        

      }


    },

      addMaleRow: function(){
        this.male_rows.push({male_record:"",male_min_days:"",male_min_months:"",male_min_years:"",male_max_days:"",male_max_months:"",male_max_years:"",male_min_ref_range:"",male_max_ref_range:"" });
      },

      removeMaleRow: function(index){
        this.male_rows.splice(index, 1);
      },

      addFemaleRow: function(){
        this.female_rows.push({female_record:"",female_min_days:"",female_min_months:"",female_min_years:"",female_max_days:"",female_max_months:"",female_max_years:"",female_min_ref_range:"",female_max_ref_range:"" });
      },

      removeFemaleRow: function(index){
        this.female_rows.splice(index, 1);
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
          if(this.$route.params.id != undefined){
            this.updateRecord(this.$route.params.id);
          }           
          this.getUnits();   
        }
      }    
    }
  }


</script>

