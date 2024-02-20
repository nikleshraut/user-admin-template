<template>
<div id="header">
    <nav role="navigation" class="navbar navbar-default navbar-fixed-top">
        <div class="container-fluid">
            <div class="row">
                <div class="navbar-header">
                    <button type="button" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" class="navbar-toggle">
                        <span class="sr-only">Toggle navigation</span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a href="#" class="navbar-brand"><span id="appname">Pathonet</span> <span id="appver">v1.0</span></a>
                </div>
                <!-- Collect the nav links, forms, and other content for toggling -->
                <div class="collapse navbar-collapse navbar-ex1-collapse">
                    <!-- <ul class="nav navbar-nav">
                        <li><a id="pMenu" href="#"><span>+</span></a></li>
                    </ul> -->
                    <form class="navbar-form navbar-left" role="search">
                        <div class="form-group">
                            <input id="pSearch" type="text" class="form-control" placeholder="To search type and hit enter">
                        </div>
                    </form>
                    <ul class="nav navbar-nav navbar-right" style="margin-right: 5px;">
                        <!-- <li class="dropdown">
                            <a href="#" class="dropdown-toggle" data-toggle="dropdown">Shree Diagnostics Pathology Labrotary <b class="caret"></b></a>
                            <ul class="dropdown-menu">
                                <li><a href="#">Other Lab if Any</a></li>
                            </ul>
                        </li> -->
                       <li v-on:click="toggleSettings"><a href="#/my-account"><i class="fa fa-cog"></i></a></li>
                        <li class="dropdown">
                            <a href="#" class="dropdown-toggle" data-toggle="dropdown">{{username}}<b class="caret"></b></a>
                            <ul class="dropdown-menu">
                                <!-- <li><a href="/#/my-account">My Account</a></li> -->
                                <li><a href="/logout">Logout</a></li>
                            </ul>
                        </li>
                    </ul>
                </div><!-- /.navbar-collapse -->
            </div>
        </div>

        {{checkAjaxCount}}
    </nav>
</div>
</template>
<script type="text/javascript">
    export default {
        mounted() {
            $("#lab_name").select2();
            $(".all-labs .select2-selection").addClass("remove-border");
            this.admin_server=this.$parent.app_domains.admin_domain;
            this.lab_server=this.$parent.app_domains.lab_admin_domain;
            this.main_domain=this.$parent.app_host.main_domain;
            this.sub_domain=this.$parent.app_host.sub_domain;
            //console.log(this.main_domain+" :: "+ this.admin_server);
            //console.log(this.sub_domain+" :: "+ this.lab_server);
            this.getAllData();
            console.log(this.$parent.logged_user);
        },
        computed: {
            username: function(){
                //return this.$parent.profile.first_name;

                if(this.$parent.logged_user.username){
                    return this.$parent.logged_user.username;
                }else{
                    return this.$parent.logged_user.first_name;
                }
            },
            profileImage: function(){
                if(this.$parent.profile.image){
                    return "/images/profile/"+this.$parent.profile.image;
                }else{
                    return "/images/no_image_female.png";
                }
            },
            checkAjaxCount: function(){
                //console.log("ajaxCount",this.ajaxCount);

                if(this.$parent.logged_user.username){
                    this.logged_user.username=this.$parent.logged_user.username;
                }
                if(this.$parent.logged_user.first_name){
                    this.logged_user.first_name=this.$parent.logged_user.first_name;
                }
                if(this.$parent.logged_user.user_type){
                    this.logged_user.user_type=this.$parent.logged_user.user_type;
                }


                //alert(this.$parent.logged_user.username+" :22: "+this.logged_user.username);
                if(this.ajaxCount == 2){
                    //$("#page_loader").hide();
                    //$("#app_content").show();

                }
            }
        },
        data: function() {
            return {
                isloading: true,
                isConsumerLoading: true,
                formLoading: false,
                main_domain:'',
                sub_domain:'',
                admin_server:'',
                lab_server:'',
                ajaxCount: 0,
                logged_user:{username:'',first_name:'',user_type:''}
            }
        },
        methods: {
            getAllData: function(){
                //Increase number of ajax count before add new method
                this.getUserProfile();
                this.getUserQualifications();

                //alert(this.$parent.logged_user.username+" :1: ");
            },
            getUserProfile: function(){
                //var profile = $("#user_detail").val();
                //$("#user_detail").val("");
                //this.$parent.profile = JSON.parse(profile);
                //this.ajaxCount++;

                //var logged_user_name=$("#logged_user_name").val();

                this.$http.get('/get_user_profile').then((response) => {
                    if(response.body.success){
                        this.$parent.profile = response.body.profile;
                        this.ajaxCount++;
                    }else{
                        console.log("error in getting user profile");
                    }
                }, (response) => {
                    console.log(response);
                });
            },
            getUserQualifications:function(){
                //console.log("getUserQualifications Started");
                //var qualifications = $("#user_qualification").val();
                //$("#user_qualification").val("");
                //this.$parent.qualifications = JSON.parse(qualifications);
                //this.ajaxCount++;
                this.$http.get('/api/user_qualifications').then((response) => {
                    this.$parent.qualifications = response.body.qualifications;
                    this.ajaxCount++;
                }, (response) => {
                    console.log(response);
                });
            },
            toggleSettings:function(){
                this.$parent.settings_menu = !this.$parent.settings_menu;
            }
        }
    }
</script>