<template>
  <div>
    <div class="inner-content">
      <div v-if="user_type==1">
        Lab Admin
      </div>

      <div v-if="user_type==4">
        Lab Staff
      </div>



      </div>
    </div>
  </template>

  <script type="text/javascript">
    export default {
      mounted() {

        /*this.host_url = window.location.hostname;
        this.current_host=this.host_url.split('.');

        if(this.current_host[1]=="pa"){
          this.lab_host_name=this.current_host[0];
        }

        this.getUserDetails();
        this.getLabDetails(this.lab_host_name);

        this.$parent.page_title = "Dashboard";
        //this.getAllPathology(false);
        this.$parent.lab_id = undefined;
        this.$parent.lab_detail = undefined;*/

      },
      data: function() {
        return {
          isloading: true,

          host_url:'',
          current_host:'',
          lab_host_name:'',
          lab_details:'',

          user_details:'',
          user_type:1,
          user_status:0
        }
      },
      methods: {

        getUserDetails: function(){
          this.$http.get('/api/user_details').then((response) => {
            this.user_details = response.body.records;
            this.user_type=this.user_details.user_type;
            this.user_status=this.user_details.user_status;

            this.isloading = false;
          }, (response) => {
            console.log(response);
          });
        },

        getLabDetails: function(lab_host_name){
          this.$http.get('/api/host_pathology/'+lab_host_name).then((response) => {
            this.lab_details = response.body.records;

            this.$parent.lab_detail=this.lab_details;
            this.$parent.lab_id=this.lab_details.id;

            this.user_type=this.user_details.user_type;
            this.user_status=this.user_details.user_status;

            this.isloading = false;
          }, (response) => {
            console.log(response);
          });
        },


    }
  }
</script>
