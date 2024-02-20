<template>
  <div class="text-center">
    <div class="padding" >
      <div class="box-header">
        <div>
          <div v-show="showloader" class="text-center loader">
            <img src="/images/loading.gif">
          </div>
          <div class="table-responsive text-left"  v-if="showPage">
            <div v-show="error" class="col-sm-12">
              <div v-bind:class="error.type" class="text-center">
                {{error.message}}
              </div>
            </div>
            <table class="table table-striped b-t b-b list-table">
              <thead>
                <tr>
                  <th width="60%" class="table-th">Patient Name</th>
                  <th width="40%" class="table-th">Action</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(report,index) in reports">
                  <td>{{report.patient.first_name}} {{report.patient.last_name}}</td>
                  <td>
                    <button v-on:click="updateFindings(report.id)" class="btn btn-primary" style="padding: 0 10px;">
                      <i class="glyphicon glyphicon-print"></i> Add Test Findings
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
     {{ ValidPageAccess }}
     <unauthorised-vue v-if="!showPage"></unauthorised-vue>
  </div>
</template>
<script>

import unauthorisedVue from '../common/unauthorised.vue';

  export default {
    mounted() {
        console.log("Print Report");
      this.$parent.showAppPage=false;
      this.$parent.page_title = "";        
/*        
        this.$parent.page_title = "Reports";
        this.getAllReports();
      */
    },
    data: function() {
        return{
            showloader: false,
            error: {},
            reports: [],
            showPage:false,
            valid_access:true,            
        }
    },
   computed: {
      ValidPageAccess: function () {
        //this.valid_access=this.$parent.valid_access;
        this.valid_access=true;
        this.checkAccess();
      }
    },    
    components: {
      'unauthorisedVue':unauthorisedVue
    },    
    methods: {
      getAllReports: function(){
        this.showloader = true;
        this.$http.get('/report_findings').then((response) => {
            this.showloader = false;
            this.reports = response.body.reports;
        }, (response) => {
            console.log(response);
        });
      },
      updateFindings: function(id){
        location.href = '#/update-findings/'+id;
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
            this.$parent.page_title = "Reports";
            this.$parent.showAppPage=true;  
            this.showPage=true;
            this.getAllReports(); 

            /*        
            this.$parent.page_title = "Reports";
            this.getAllReports();
            */            
                        
          }
       }  

    }

  }
</script>
