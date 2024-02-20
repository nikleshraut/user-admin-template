<template>
    <div class="text-center">
        <div class="padding">
    <div class="box-header">
      <!-- / .modal -->
<div >
    <div v-show="showloader" class="text-center loader">
        <img src="/images/loading.gif">
    </div>

    <div class="table-responsive text-left"  v-if="showPage">
        <div v-show="error" class="col-sm-12">
            <div v-bind:class="error.type" class="text-center">
                {{error.message}}
            </div>
        </div>
      <table class="table table-striped b-t b-b list-table" >
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
                <button v-if="report.approved_at" v-on:click="getPdf(report.id)" data-toggle="modal" data-target="#add-consumer" class="btn btn-primary" style="padding: 0 10px;">
                    <i class="glyphicon glyphicon-print"></i> Print
                </button>
                <button v-else v-on:click="editReport(report.id)" class="btn btn-primary" style="padding: 0 10px;">
                    <i class="glyphicon glyphicon-edit"></i> Edit
                </button>
                <button v-if="!report.approved_at" data-toggle="modal" data-target="#view_report" v-on:click="viewReport(report.id)" class="btn btn-primary" style="padding: 0 10px;">
                    <i class="glyphicon glyphicon-edit"></i> view and approve
                </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  </div>
</div>



        <iframe id="iFramePdf" v-bind:src="pdfurl" style="display:none;"></iframe>

        <!-- Consumer Modal -->
        <div class="modal fade in" id="add-consumer" role="dialog" aria-labelledby="exampleModalLabel" style="display: none; padding-left: 17px;">
          <div class="col-sm-10 col-sm-offset-1">
              <!-- modal-content -->
              <div class="modal-content" style="border-radius:0px;">
                <!-- modal-header -->
                <div class="modal-header">
                  <div class="col-sm-6 text-left">
                    <h4 class="modal-title">Preview</h4>
                  </div>
                  <div class="col-sm-6 text-right">
                    <button v-if="showprintbtn" v-on:click="printPdf" style="padding: 0 10px; margin:0 10px;" class="btn btn-primary"><i class="glyphicon glyphicon-print"></i> Print</button>

                    <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">X</span></button>
                  </div>
                </div>
                <!--/ modal-header -->
                <div v-show="showloader" class="text-center">
                  <img src="/images/loading.gif" >
                </div>
                <!-- modal-body -->
                <div v-show="!showloader" class="">
                  <object v-bind:data="pdfurl" type="application/pdf" style="width:100%;height:500px;">
                      <!-- <a v-bind:href="pdfurl">test.pdf</a> -->
                  </object>
                </div>
                <!--/ modal-body -->
              </div>
              <!--/ modal-content -->
          </div>
        </div>
        <!-- Consumer Modal -->
        <!--  -->
        <div class="modal fade in" id="view_report" role="dialog" aria-labelledby="exampleModalLabel">
          <div class="col-sm-8 col-sm-offset-2 modal-dialog-30">
            <div class="modal-content" style="min-height:400px;">
              <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                <h4 class="modal-title">Approve Report</h4>
                <button v-on:click="approveReport" class="btn btn-primary" style="padding: 0 10px;">
                    <i class="glyphicon glyphicon-edit"></i> approve
                </button>
              </div>
              <div class="modal-body">
                 <div v-show="showloader" class="text-center loader">
                    <img src="/images/loading.gif">
                </div>
                <div>
                  {{(error.message) ? error.message : "" }}
                </div>
                <div v-html="report_html"></div>
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
      // this.$parent.showAppPage=false;
      this.$parent.page_title = "Print Report";

/*        console.log("Print Report");
        this.$parent.page_title = "Print Report";
        this.getAllTemplates();
        console.log(this.$options.name);*/
    },
    data: function() {
        return{
            showloader: false,
            showprintbtn: false,
            pdfurl: "",
            reports: [],
            error: {message:""},
            report_html:"",
            report : {},
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
        viewReport: function(report_id){
            this.showloader = true;
            this.report_html = "";
            this.$http.get('/view_report_as_html/'+report_id).then((response) => {
              if(response.body.success){
                this.report.id = report_id;
                this.report_html = response.body.html;
                this.showloader = false;
              }
            }, (response) => {
                console.log(response);
            });
        },
        approveReport: function(){
          this.showloader = true;
          var report_id = this.report.id
            this.$http.post('/approve_report',{id:report_id}).then((response) => {
              if(response.body.success){
                this.error = {message:"Approved successfully"};
                this.showloader = false;
                this.getAllTemplates();
              }
            }, (response) => {
                console.log(response);
            });
        },
        getAllTemplates: function(){
            this.showloader = true;
            this.$http.get('/report_masters').then((response) => {
                this.showloader = false;
                this.reports = response.body.reports;
                this.error = {message:""};
            }, (response) => {
                console.log(response);
            });

       },
        printPdf: function (){
          var getMyFrame = document.getElementById("iFramePdf");
          getMyFrame.focus();
          getMyFrame.contentWindow.print();
        },
        getPdf: function (report_id){
            this.showloader = true;
            this.$http.get('/print-report/'+report_id,{},{responseType: 'arraybuffer'}).then(function (response) {
                var content_type = response.headers.get('Content-Type');
                console.log(); //application/json && application/pdf
                if(content_type == 'application/pdf'){
                  var blob = new Blob([response.data], {type: 'application/pdf'});
                  this.pdfurl = window.URL.createObjectURL(blob)+"#view=FitW";
                  this.showloader = false;
                  /* display print button other than firefox */
                  if (navigator.userAgent.indexOf("Firefox") == -1) {
                    this.showprintbtn = true;
                  }
                }else if(content_type == 'application/json'){
                  this.showloader = false;
                  $("#add-consumer").modal('hide');
                  console.log(response.body.message);
                }else{
                  this.showloader = false;
                  $("#add-consumer").modal('hide');
                  console.log("Invalid header type");
                }
            });
        },
        editReport: function(report_id){
            console.log("editReport: "+report_id);
            location.href = '#/test-registration/'+report_id;
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
            this.$parent.page_title = "Print Report";
            this.$parent.showAppPage=true;  
            this.showPage=true;
            this.getAllTemplates();

            //this.getRecords(); 
            /*        
            console.log("Print Report");
            this.$parent.page_title = "Print Report";
            this.getAllTemplates();
            console.log(this.$options.name);
            */            
                        
          }
       }         
    }
  }
</script>
