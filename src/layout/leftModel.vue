<template>
  <div class="left_box">
      <h3 class="left_title">
        <span v-show="leftHide||floor_nav">组件库</span>
        <i @click.stop="switchFn" class="icon iconfont icon-zhankai icon-white" :class="leftHide?'icon-shouqi':''"></i>
      </h3>
      <div class="left_scroll">
        <ul class="tab_nav" @click="tabClick">
          <li v-for="(tabItem,index) in tabTree" :class="tabIndex==index?'active':''">
            <i class="icon iconfont tabIcon" :class="tabItem.icon"></i>
            <p>{{tabItem.text}}</p>
          </li>
        </ul>
        <p class="list_length" v-show="leftHide" v-if="widgets.length">全部（{{widgets.length}}）</p>
        <draggable v-show="leftHide" :options="dragOptions" class="leftModel" :list="widgets" >
          <p class="nolist" v-if="!widgets.length">暂无相关组件</p>
          <div v-else v-for="widget in widgets" class="widget-card" :wtype="widget.w" :type="widget.placeholder.type" :model_id="widget.model_id" :close="widget.isClose">
            <i class="icon iconfont" :class="widget.icon"></i>
            <p>{{widget.name}}</p>
          </div>
        </draggable>
        <div v-if="floor_nav" class="leftModel">
          <tree :json="site.children"></tree>
        </div>
      </div>

  </div>
</template>

<script>
  import { mapState, mapActions , mapMutations} from 'vuex'
  import Draggable from 'vuedraggable'
  import Tree from '~/common/jsonTree'
    export default {
      name: "leftModel",
      components: {
        Draggable,
        Tree
      },
      data(){
          return {
            tabIndex:0,
            leftHide: false,
            floor_nav:false,
            dragOptions: {
              group: {
                name: 'widgets',
                pull: 'clone',
                put:false,
              },
              sort: false,
              forceFallback: true
            },
          }
      },
      computed: {
        ...mapState(['tabTree','widgets','site']),
      },
      mounted(){
        this.setWidget(this.tabTree[0].list)
        this.leftHide=true;
        $('.left_scroll,.tab_nav,.leftModel').css("maxHeight",$(window).height()-70+'px');
        window.onresize=function(){
          $('.left_scroll,.tab_nav,.leftModel').css("maxHeight",$(window).height()-20+"px");
        }
        $(window).scroll(function(){
          var scrollTop = $(window).scrollTop();
          if(scrollTop+$(window).height()>$('body').height()+80){
            return false
          }
          if(scrollTop < 80)
            $(".left_box").css('top', '80px');
          else
            $(".left_box").css('top', scrollTop +'px');
            $('.left_scroll,.tab_nav,.leftModel').css("maxHeight",$(window).height()-32+"px");
        });
      },
      methods:{
        ...mapMutations(['setWidget']),
        ...mapActions(['getSite','getDefaultConfig']),
        tabClick(e){
          let i=$(e.target).parent().index();
          this.tabIndex=i
          if(this.tabTree[i].attrId==1){
            this.setWidget(this.tabTree[i].list)
            this.floor_nav=false;
            this.leftHide=true;
          }else{
            this.leftHide=false;
            this.floor_nav=true;
          }

        },
        switchFn(){
          this.leftHide?this.leftHide=false:this.leftHide=true;
        },
      }
    }
</script>

<style lang="less">
.left_box {
  background-color:#fff;
  position:absolute;
  top:70px;left:0;overflow:hidden;
  max-width:280px;
  .left_title{
    color:#fff;
    background-color:#5E9EF3;
    display:flex;
    justify-content: space-between;
    padding:6px 8px;
    span{
      display: block;width: 80%;
    }
    i{
      text-align: center;flex: 1;
    }
  }
  .left_scroll{

  }
  .tab_nav{
    width:70px;text-align:center;border-right:1px solid #F0F0F0;float:left;color:#666;overflow:auto;
    li{
      padding:5px 0;
      &:hover i,&:hover p{
        color:#5E9EF3;curpor:pointer;
      }
    }
    .active{
      background-color:#5E9EF3;color:#fff;
      .iconfont{color:#fff }
      &:hover i,&:hover p{
        color:#fff;
      }
    }
    .tabIcon{
      font-size:24px;text-align:center;display:block;width:100%;padding:4px 0;
    }
  }
  .list_length{
    width:170px;float:left;display:inline-block;fonn-size:12px;color:#D3D3D3;line-height:16px;padding:6px 5px;box-sizing:border-box;
  }
  .leftModel{
    width:170px;overflow-y: auto;display:flex;flex-wrap: wrap;justify-items: baseline;
    .nolist{color:#666;display:block;width:100%;padding:40px 30px;text-align:center;}
  }
  .widget-card {
    border-radius: 2px;
    width: 85px;margin:6px 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    cursor: move;
    i {
      display: block;
      font-size: 2em;
    }
    p {
      margin-top: 5px;
      margin-bottom: 5px;color:#666;
    }
    &:hover i,&:hover p{
      color:#5E9EF3;
    }
  }
}
</style>
