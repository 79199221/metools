<template>
     <div class="layui-layout layui-layout-admin"  id="app">
        <div class="layui-header ymheader">
            <a href="/">
                <img class="logo layui-circle" v-if="siteInfo.LogoUrl" :src="siteInfo.LogoUrl">
                <img class="logo layui-circle" v-else src="./assets/images/logo.png">
            </a>
            <div class="searchTxt">
                <input type="text" class="layui-input" placeholder="搜索" @keyup.enter="search"/>
            </div>
            <div class="user-action" v-if="siteInfo.SiteName">
               <a href="/" v-text="siteInfo.SiteName"></a>
            </div>
            <div class="user-action" v-else>
               <a href="/">个人工具站 - By </a> <a href="/" target="_blank">易墨</a>
            </div>
        </div>
        <div class="layui-side layui-bg-black" id="leftMenu">
            <div class="layui-side-scroll">
                <v-header :menuType="1" :menuModel="menuItems" :allOpen="true"></v-header>
            </div>
        </div>
        <div class="layui-body layui-tab-content" id="rightContent">
            <router-view></router-view>
        </div>
        <div class="layui-footer footer footer-doc" id="contentFooter" >
            <p v-if="siteInfo.FooterLinks && siteInfo.FooterLinks.length>0">
                <a :href="item.Url" v-text="item.Title" v-for="item in siteInfo.FooterLinks"  target="_blank"></a>
                <a @click="openMsg">少年需要留个言么？</a>
            </p>
        </div>
    </div>
</template>
<script>
export default {
    data (){
      return {
          menuItems:[],
          siteInfo:{
              LogoUrl:'',
              SiteName:'',
              FooterLink:[]
          }
      }
    },
    methods:{
        search(){
            layer.msg('不要了吧，这么点功能还搜索，后面再说~')
        },
        openMsg(){
            layer.open({            
                type:2,
                shade:0,
                skin:'layui-layer-molv',
                anim: 4,
                offset:'rb',
                area: ['400px', '500px'],
                title:'我是谁？',
                content:'/static/msg.html'});
        }
    },
    created(){
        this.siteInfo=window.siteData.SiteInfo;
        this.menuItems=window.siteData.MenuItems;
    }
}
</script>