<template>
    <div style="padding: 10px 0;">
    <div v-if="showPage">
        <div v-show="showloader" class="text-center loader">
            <img src="/images/loading.gif">
        </div>
        <div class="progress">
          <div class="progress-bar progress-bar-success" role="progressbar" style="width:33.33%">
            1. Lab Info
          </div>
          <div class="progress-bar progress-bar-striped active progress-bar-warning" role="progressbar" style="width:33.33%">
            2. Lab Users
          </div>
          <div class="progress-bar  progress-bar-warning" role="progressbar" style="width:33.33%">
            3. Licence
          </div>
        </div>

        <div class="row form-group">
          <div class="col-sm-3">
            <button v-bind:disabled="!previous" type="button" v-on:click="addLab" class="btn btn-default col-sm-12">&lt;&lt; Previous</button>
          </div>
          <div class="col-sm-3 col-sm-offset-6">
            <button v-bind:disabled="!next" type="button" v-on:click="addLicence" class="btn btn-default col-sm-12">Next &gt;&gt;</button>
          </div>
        </div>

        <div class="row panel">
            <div class="col-md-9">
                <h4>Add Lab Users</h4>
            </div>
            <div class="col-md-3 pull-right padding-tb">
                <span class="required_field"> *</span> denotes required field
            </div>
        </div>
        <form class="form-body" role="form" method="POST" v-on:submit.prevent="submitForm">
            <!-- Enable to display all error all together -->
            <!-- <div class="row">
                <div class="col-md-12 form-group licence-form-group">
                    <div v-bind:class="{ 'text-danger': errors }">
                        <span v-for="err in errors">
                            {{err[0]}}<br/>
                        </span>
                    </div>
                </div>
            </div> -->
            <div class="row">
                <div class="col-md-3 form-group">
                    Username
                </div>
                <div class="col-md-3 form-group">
                    Password
                </div>
                <div class="col-md-3 form-group">
                    Role
                </div>
            </div>
            <div class="hide">
                <input class="hide" type="text" name="username" value="">
                <input class="hide" type="text" name="password" value="">
            </div>
            <div class="row" v-for="(user,index) in users">
                <div class="col-md-3 form-group username-form-group">
                    <input class="md-input" placeholder="Username" v-model="user.username" type="text" required="">
                    <div class="text-danger">
                        {{errors['users.'+index+'.username']?errors['users.'+index+'.username'][0]:""}}
                    </div>
                </div>
                <div class="col-md-3 form-group password-form-group">
                    <input class="md-input" placeholder="Password" v-model="user.password" type="password" required="">
                    <div class="text-danger">
                    {{errors['users.'+index+'.password']?errors['users.'+index+'.password'][0]:""}}
                    </div>
                </div>
                <div class="col-md-3 form-group user-roles" v-bind:class="'row_'+index">
                    <select v-bind:id="index" class="md-input applySelect2" v-model="user.role" required="">
                      <option value="">Select Role</option>
                      <option value="lab_admin">Lab Admin</option>
                      <option value="3">Reception</option>
                      <option value="4">Operator</option>
                      <option value="5">Reviewer/Doctor</option>
                    </select>
                    <div class="text-danger">
                    {{errors['users.'+index+'.role']?errors['users.'+index+'.role'][0]:""}}
                    </div>
                </div>
                <div class="col-md-3 form-group password-form-group">
                    <button type="button" v-on:click="removeRow(index)" style="padding: 0 10px;" class="btn btn-danger">
                        <i class="glyphicon glyphicon-trash"></i> Remove
                    </button>
                    <button v-if="users.length == index+1" type="button" v-on:click="addRow" style="padding: 0 10px;" class="btn btn-primary">
                        <i class="glyphicon glyphicon-plus"></i> Add More
                    </button>
                </div>
            </div>
            <div class="row">
                <div class="col-sm-3 col-sm-offset-6 text-center">
                    <button type="submit" class="btn btn-primary col-sm-12">Submit</button>
                </div>
            </div>
        </form>
        </div>
<!-- {{ ValidPageAccess }}
<unauthorised-vue v-if="!showPage"></unauthorised-vue> -->

    </div>

</template>

<script>
    import unauthorisedVue from '../common/unauthorised.vue';

    export default {
        mounted() {
            //this.$parent.showAppPage=false;
            //this.$parent.page_title = "";

            console.log('Add Lab User');
            this.$parent.page_title = "Lab Users";
            if(!this.lab_id){
                //location.href = '#/add-lab';
                console.log("no lab_id");
            }else{
                if(this.$parent.lab_detail == undefined){
                    this.updatelab(this.lab_id);
                }else{
                    if(this.$parent.lab_detail.lab_users){
                        console.log("lab_users",this.$parent.lab_detail.lab_users);
                        this.users = JSON.parse(this.$parent.lab_detail.lab_users);
                        this.next = true;
                    }
                    this.previous = true;
                }
                var vm = this;
                setTimeout(function() {
                    vm.applySelect2();
                }, 1);
            }
        },
        data: function(){
            return {
                users: [{username:"",password:"",role:""}],
                lab_id: this.$parent.lab_id,
                formData:[],
                previous:false,
                next:false,
                showloader: false,
                errors: "",
                showPage:true,
                valid_access:false,

            }
        },
        computed:{


            /*ValidPageAccess: function () {

                if(this.$parent.valid_access==true){
                    if(this.$parent.app_host.sub_domain==this.$parent.app_domains.lab_admin_domain){
                        this.$parent.valid_access=false;
                    }
                }
                this.valid_access=this.$parent.valid_access;
                this.checkAccess();
            }*/

        },
        components: {
          'unauthorisedVue':unauthorisedVue
        },
        methods:{
            submitForm: function(){
                this.errors = "";
                this.showloader =  true;
                this.formData = {lab_id:this.lab_id,users:this.users};
                this.$http.post('/api/add_lab_user',this.formData).then((response) => {
                    console.log(response.body);
                    this.showloader =  false;
                    if(response.body.isvalid){
                        if (response.body.success) {
                            this.$parent.lab_detail.lab_users = JSON.stringify(this.users);
                            location.href = '#/add-licence';
                        }
                    }else{
                        this.errors = response.body.errors;
                    }
                }, (response) => {

                });
            },
            updatelab: function(lab_id){
                this.$http.get('/api/pathology/'+lab_id).then((response) => {
                    this.$parent.lab_detail = response.body.lab_detail;
                    this.$parent.lab_detail.id = lab_id;
                    if(this.$parent.lab_detail.lab_users){
                        this.users = JSON.parse(this.$parent.lab_detail.lab_users);
                        this.next = true;
                    }
                    this.previous = true;
                }, (response) => {
                    console.log(response);
                });
            },
            addLab: function(){
                location.href = '#/add-lab';
            },
            addLicence: function(){
                location.href = '#/add-licence';
            },
            addRow: function(){
                var index = Object.keys(this.users).length;
                this.users.push({username:"",password:"",role:""});
                var vm = this;
                setTimeout(function(){
                    $("#"+index).select2()
                    .on("change", function(){
                        console.log($(this).val() + " selected");
                        var value= $(this).val();
                        var id = $(this).attr('id');
                        vm.users[id].role = value;
                    });
                    $(".row_"+index+" .select2-container").addClass("form-select2 text-left").css('width','100%');
                    $(".row_"+index+" .select2-selection").css("border","none");
                    $(".row_"+index+" .select2-selection__rendered").css("padding","0px");
                },1);
            },
            removeRow: function(index){
                if (this.users.length > 1) {
                    $(".applySelect2").select2("destroy");
                    this.users.splice(index, 1);
                    var vm = this;
                    setTimeout(function(){
                        vm.applySelect2();
                        /*$(".applySelect2").select2();
                        $(".user-roles .select2-container").addClass("form-select2 text-left").css('width','100%');
                        $(".user-roles .select2-selection").css("border","none");
                        $(".user-roles .select2-selection__rendered").css("padding","0px");*/
                    },1);
                }
            },
            applySelect2: function(){
                console.log("applySelect2 called");
                var vm = this;
                $(".applySelect2").select2()
                .on("change", function(){
                    console.log($(this).val() + " selected");
                    var value= $(this).val();
                    var id = $(this).attr('id');
                    vm.users[id].role = value;
                });
                $(".user-roles .select2-container").addClass("form-select2 text-left").css('width','100%').css('max-width','400px');
                $(".user-roles .select2-selection").css("border","none");
                $(".user-roles .select2-selection__rendered").css("padding","0px");
            },

            checkAccess: function(){

                if(this.valid_access==''){
                  this.$parent.validAccess();
                }

                if(this.valid_access==true ){
                    this.showloader =  false;
                    this.$parent.page_title = "Lab Users";
                    //this.$parent.back_url = "#/";
                    //this.$parent.help_content = "<h4>My Pathology</h4> help content";

                    this.$parent.showAppPage=true;
                    this.showPage=true;
                    //this.getAllLabs();

                    if(!this.lab_id){
                        location.href = '#/add-lab';
                    }else{
                        if(this.$parent.lab_detail == undefined){
                            this.updatelab(this.lab_id);
                        }else{
                            if(this.$parent.lab_detail.lab_users){
                                console.log("lab_users",this.$parent.lab_detail.lab_users);
                                this.users = JSON.parse(this.$parent.lab_detail.lab_users);
                                this.next = true;
                            }
                            this.previous = true;
                    }
                    var vm = this;
                    setTimeout(function() {
                        vm.applySelect2();
                        }, 1);
                    }
                }
            }
        }
    }
</script>