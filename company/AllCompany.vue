<template>
 <div>


<div class="padding" >
    <div class="box-header">
      <!-- / .modal -->
<div >
    <div v-show="showloader" class="text-center loader">
        <img src="/images/loading.gif">
    </div>

    <div class="table-responsive" v-if="showPage">
        <div v-show="error" class="col-sm-12">
            <div v-bind:class="error.type" class="text-center">
                {{error.message}}
            </div>
        </div>
        <div class="col-sm-12 form-group">
            <button v-on:click="addcompany" class="row btn btn-primary btn-sm pull-right">Add new company</button>
        </div>
      <table class="table table-striped b-t b-b list-table">
        <thead>
          <tr>
            <th width="25%" class="table-th">Company Name</th>
            <th width="25%" class="table-th">Address</th>
            <th width="25%" class="table-th">Phone</th>
            <th width="25%" class="table-th">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(company,index) in company">
            <td class="table-td">{{company.name}}</td>
            <td class="table-td">{{company.address}}</td>
            <td class="table-td">{{company.phone}}</td>
            <td class="table-td">
                <button v-on:click="updatecompany(company.id)" class="btn btn-primary" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-edit"></i> Edit
                </button>
                <button data-toggle="modal" data-target="#confirmDelete" v-on:click="setcompanyToDelete(company.id)" class="btn btn-danger" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-trash"></i> Delete
                </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
  {{ ValidPageAccess }}
  </div>
</div>

<delete-vue></delete-vue>
<unauthorised-vue v-if="!showPage"></unauthorised-vue>
  </div>
</template>
<script>
  import deleteVue from '../common/delete.vue';
  import unauthorisedVue from '../common/unauthorised.vue';


  export default {
    mounted() {
      this.$parent.showAppPage=false;
      this.$parent.page_title = "";

        /*console.log("All Companys");
        this.$parent.page_title = "All Companys";
        if($.isEmptyObject(this.$parent.company)){
            this.getAllCompanys();
        }
        */
    },
    data: function() {
        return {
            showloader: false,
            error:{},
            company: this.$parent.company,
            types:{"1":"Header","2":"Footer","3":"Invoice"},
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
      'deleteVue': deleteVue,
      'unauthorisedVue':unauthorisedVue
    },
    methods: {
        addcompany: function(){
         location.href = '#/add-company';
       },
       getAllCompanys: function(){
            this.showloader = true;
            this.$http.get('/company').then((response) => {
                this.showloader = false;
                this.company = response.body.company;
                this.$parent.company = this.company;
            }, (response) => {
                console.log(response);
            });

       },
       updatecompany: function(company_id){

            location.href = '#/company/'+company_id;
       },
       setcompanyToDelete: function(company_id){
            this.company_id_to_delete = company_id;
       },
       deleteRecord: function(){
            this.showloader = true;
            var company_id = this.company_id_to_delete;
            this.$http.delete('/company/'+company_id).then((response) => {
                this.showloader = false;
                console.log(response.body.success);
                if(response.body.success){
                    if(!this.showloader){
                        this.error = {'type':'text-success','message':'Updated successfully.'};
                    }
                    this.getAllCompanys();
                }else{
                    this.error = {'type':'text-danger','message':response.body.message};
                    console.log(response.body);
                }
            }, (response) => {
                console.log(response);
            });
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
              this.$parent.page_title = "All Companys";
              this.$parent.showAppPage=true;  
              this.showPage=true;
              if($.isEmptyObject(this.$parent.company)){
                this.getAllCompanys();
              }


            }
       }       


    }
  }
</script>
