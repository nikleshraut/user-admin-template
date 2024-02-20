<template>
  <div id="sidebar">
  <div class="nav-side-menu">
    <p v-show="settings_menu">Settings</p>
    <p v-show="!settings_menu">Dashboard</p>
    <div v-show="settings_menu">
      <div v-on:click="toggleSettings" class="lab-back-link"> &larr; Back </div>
      <ul>
        <li><a href="/#/my-account" class=""><i class="pon pon-dashboard"> User Profile </i></a></li>
        <li><a href="/#/app-type" class=""><i class="pon pon-dashboard"> App Type </i></a></li>
        <li><a href="/#/" class=""><i class="pon pon-dashboard"> Preference </i></a></li>
        <li><a href="/#/" class=""><i class="pon pon-dashboard"> Subscription </i></a></li>
      </ul>
    </div>
    <div v-show="!settings_menu">
        <ul>
          <template v-for="(menu,index) in menus">
              <li>
                <router-link v-bind:to="menu.address">
                    <i v-bind:class="menu.icon"> {{menu.name}} </i>
                </router-link>
              </li>
          </template>
        </ul>
    </div>
   <!-- <div>User Role : {{user_role}}</div> -->
  </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        menus: {},
        user_role: "",
        roles: [],
        loading: true
      }
    },
    mounted() {
      this.$parent.appLoaded = false;
      $("#menu-content li a").on("click",function(){
        $("#menu-content li").removeClass("active");
        $(this).parent().addClass("active");
      });
      this.getMenu();


      /*this.loading = true;
      this.$http.get('/api/user_roles_and_menus').then((response) => {
          this.loading = false;
          console.log(response.body);
          this.roles = response.body.roles;
          setTimeout(function () {
            var app = this;
            $("#user_role").select2();
            $(".user_role .select2-selection").addClass("remove-border");
            $("#user_role").on("change",function(){
              console.log($(this).val() + " selected");
              app.user_role = $(this).val();
            });
          }, 100);
        }, (response) => {

      });


      */
    },
    computed:{
        settings_menu: function(){
            return this.$parent.settings_menu;
        }
    },
    methods:{
      getMenu: function(){
        this.$http.get('/get-menu').then((response) => {
            this.menus = response.body.menus;
            setTimeout(function () {
                $("#menu-content li").on("click",function(){
                  $("#menu-content li").removeClass("active");
                  $(this).addClass("active");
                });
            }, 100);
            this.$parent.appLoaded = true;
          }, (response) => {
        });
      },
      toggleSettings:function(){
            this.$parent.settings_menu = !this.$parent.settings_menu;
            location.href = '#'+this.menus[0].address;
        }
    }
  }
</script>
