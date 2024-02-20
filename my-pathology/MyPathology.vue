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
                    <div class="col-sm-12 form-group page-sub-title">
                        List of Pathology
                    </div>
                </div>
                <div style="position:absolute;right:10px;bottom:10px;">
                    <span style="
                            width: 56px;
                            height: 56px;
                            line-height: 56px;
                            font-size: 20px;
                            color: #fff;
                            " class="btn no-padding btn-primary"><i class="fa fa-plus"></i></span>
                </div>
                <div class="col-sm-12" v-for="lab in labs">
                        <div class="col-sm-12">
                            <hr style="">
                        </div>
                        <div class="col-sm-12" style="height:134px;">
                            <div class="col-sm-2" style="height: 100%;align-items: center;display: flex;">
                                <span class="btn no-padding" style="
                                background-color: #ccc;
                                width: 64px;
                                height: 64px;
                                line-height: 64px;
                                font-size: 32px;
                                color: #000;
                                "><i class="fa fa-hospital-o"></i>
                                </span>
                            </div>
                            <div class="col-sm-4">
                                <div><strong class="text-bold">{{lab.lab_name}}</strong></div>
                                <div>{{lab.address_area}}, {{lab.city}}</div>
                                <div>{{lab.district}}, {{lab.pincode}}</div>
                                <div>{{lab.owner_contact_no}}</div>
                                <div>http://{{lab.lab_host_name}}{{$parent.app_host.main_domain}}</div>
                                <div>Status : <span v-bind:class="lab.lab_status?'text-primary':'text-danger'">{{lab.lab_status?'Active':'Setup Incomplete'}}</span></div>
                            </div>
                            <div class="col-sm-3 text-right" style="height: 100%;display: flex;align-items: flex-end;">
                                <div><button type="button" class="btn btn-default">Detail</button></div>
                            </div>
                            <div class="col-sm-3" style="height: 100%;display: flex;align-items: flex-end;">
                                <div>
                                    <div class="form-group text-center">
                                        <button style="0 10px 12px -10px #125889" type="button" class="btn btn-primary form-group">Go to Lab</button>
                                    </div>
                                    <div class="text-center">
                                        <button style="0 10px 12px -10px #125889" type="button" class="btn btn-primary form-group">Edit Lab</button>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="col-sm-12">
                            <hr style="">
                        </div>
                </div>
            </div>
        </div>
    </form>
</template>
<script>
  import deleteVue from '../common/delete.vue';

  export default {
    mounted() {
        console.log("All Doctors");
        this.$parent.page_title = "My Pathology";
        if($.isEmptyObject(this.$parent.labs)){
            this.getAllLabs();
        }
    },
    data: function() {
        return {
            showloader: false,
            error:{},
            labs: this.$parent.labs,
            types:{"1":"Header","2":"Footer","3":"Invoice"},
        }
    },
    components: {
      'deleteVue': deleteVue
    },
    methods: {
        adddoctor: function(){
         location.href = '#/add-doctor';
       },
       getAllLabs: function(){
            this.showloader = true;
            this.$http.get('/api/pathologies').then((response) => {
                this.showloader = false;
                this.labs = response.body.records;
                this.$parent.labs = this.labs;
            }, (response) => {
                console.log(response);
            });

       },
       updatedoctor: function(doctor_id){
            location.href = '#/doctor/'+doctor_id;
       },
       setdoctorToDelete: function(doctor_id){
            this.doctor_id_to_delete = doctor_id;
       },
       deleteRecord: function(){
            this.showloader = true;
            var doctor_id = this.doctor_id_to_delete;
            this.$http.delete('/doctors/'+doctor_id).then((response) => {
                this.showloader = false;
                console.log(response.body.success);
                if(response.body.success){
                    if(!this.showloader){
                        this.error = {'type':'text-success','message':'Deleted successfully.'};
                    }
                    this.$parent.doctors = {};
                    //this.getAllDoctors();
                }else{
                    this.error = {'type':'text-danger','message':response.body.message};
                    console.log(response.body);
                }
            }, (response) => {
                console.log(response);
            });
       }


    }
  }
</script>
