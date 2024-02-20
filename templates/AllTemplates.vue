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
            <button v-on:click="addTemplate" class="row btn btn-primary btn-sm pull-right">Add new template</button>
        </div>
      <table id="template_tbl" class="table table-striped b-t b-b list-table">
        <thead>
          <tr>
            <th width="60%" class="table-th">Template Name</th>
            <th width="20%" class="table-th">Default Template</th>
            <th width="20%" class="table-th">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(template,index) in templates">
            <td class="table-td">{{template.name}}</td>
            <td class="table-td">{{template.is_default?"Yes":"No"}}</td>
            <td class="table-td">
                <button v-on:click="updateTemplate(template.id)" class="btn btn-primary" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-edit"></i> Edit
                </button>
                <button v-if="!template.is_default" data-toggle="modal" data-target="#confirmDelete" v-on:click="setTemplateToDelete(template.id)" class="btn btn-danger" style="padding: 0 10px;">
                  <i class="glyphicon glyphicon-trash"></i> Delete
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

        /*console.log("All Templates");
        this.$parent.page_title = "All Templates";
        if($.isEmptyObject(this.$parent.templates)){
            this.getAllTemplates();
        }
        */
    },
    data: function() {
        return {
            showloader: false,
            error:{},
            templates: this.$parent.templates,
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
        addTemplate: function(){
         location.href = '#/add-template';
       },
       getAllTemplates: function(){
            this.showloader = true;
            this.$http.get('/templates').then((response) => {
                this.showloader = false;
                this.templates = response.body.templates;
                this.$parent.templates = this.templates;
                setTimeout(function() {
                    $("#template_tbl").dataTable();
                }, 100);

            }, (response) => {
                console.log(response);
            });

       },
       updateTemplate: function(template_id){

            location.href = '#/template/'+template_id;
       },
       setTemplateToDelete: function(template_id){
            this.template_id_to_delete = template_id;
       },
       deleteRecord: function(){
            this.showloader = true;
            var template_id = this.template_id_to_delete;
            this.$http.delete('/templates/'+template_id).then((response) => {
                this.showloader = false;
                console.log(response.body.success);
                if(response.body.success){
                    if(!this.showloader){
                        this.error = {'type':'text-success','message':'Updated successfully.'};
                    }
                    this.getAllTemplates();

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
              this.showloader =  false;
              this.$parent.page_title = "All Templates";
              this.$parent.showAppPage=true;  
              this.showPage=true;
              this.getAllTemplates();

            /*console.log("All Templates");
            this.$parent.page_title = "All Templates";
            if($.isEmptyObject(this.$parent.templates)){
                this.getAllTemplates();
            }
            */                          
          }
       }       


    }
  }
</script>
