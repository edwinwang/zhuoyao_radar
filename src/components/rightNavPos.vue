<template>
  <div class="setting-nav">
    <div class="burger-wrap  burger-wrap-pos">
      <div :class="['nav-burger', showMenu ? 'active' : '']">
        <div class="setting-icon" @click.prevent="openMenu">
          <i v-if="showMenu" class="el-icon-d-arrow-right"></i>
          <i v-else class="el-icon-setting"></i>
        </div>
      </div>
    </div>
    <transition name="black">
      <div class="black-screen" v-show="showMenu" @click.prevent="openMenu"></div>
    </transition>
    <transition name="side">
      <!-- gentime: 1560670000
latitude: 30199608
lifetime: 3600
longtitude: 120086050
sprite_id: 2000324 -->
      <div class="side-nav" v-show="showMenu">
        <el-table ref="table" :data="tableData" :height="tableHeight">
          <el-table-column label="妖灵">
            <template slot-scope="scope">
              {{monsterName[scope.row.sprite_id] || scope.row.sprite_id}}
            </template>
          </el-table-column>
          <el-table-column label="坐标">
            <template slot-scope="scope">
              <el-button class="btnCopy" :data-clipboard-text="posVal" @click="handleCopy('ios', scope.row.longtitude, scope.row.latitude)">ios</el-button>
              <!-- <el-button class="btnCopy" :data-clipboard-text="posVal" @click="handleCopy('android', scope.row.longtitude, scope.row.latitude)">android</el-button> -->
            </template>
          </el-table-column>
          <el-table-column label="时间">
            <template slot-scope="scope">
              {{formatRemainTime(scope.row.gentime, scope.row.lifetime)}}
            </template>
          </el-table-column>
        </el-table>
      </div>
    </transition>
  </div>
</template>
<script>
import { getLocalStorage } from "../lib/util"
import MonsterConfig from "../lib/tempdata"
import clipboard from 'clipboard'
export default {
  name: "radar-right-nav-pos",
  props: {
    showMenu: {
      type: Boolean,
      default: false
    },
    tableData:{
      type: Array,
      default: []
    }
  },
  data() {
    return {
      activeName: "1",
      clipboardBtn: null,
      posVal: '',
      monsterName: {},
      tableHeight: 50
    };
  },
  mounted(){
    this.clipboardBtn = new clipboard('.btnCopy');
    for (const item of MonsterConfig.Data){
      this.monsterName[item.Id] = item.Name
    }
    this.$nextTick(function () {
            this.tableHeight = window.innerHeight - this.$refs.table.$el.offsetTop - 50;
            
            // 监听窗口大小变化
            let self = this;
            window.onresize = function() {
                self.tableHeight = window.innerHeight - self.$refs.table.$el.offsetTop - 50
            }
        })
  },
  methods: {
    openMenu() {
      this.$emit("update:showMenu", !this.showMenu);
    },

    handleCopy(tp, long, la){
      if (tp === 'ios'){
        this.posVal = (long+6756)/1000000 + ',' + (la+6033)/1000000
      } else {
        this.posVal = (la+2074)/1000000 + ',' + (long-4372)/1000000
      }
      //console.log(tp, long, la, this.posVal)
    },
    formatRemainTime(gentime, lifetime){
      const now = Date.parse(new Date())/1000
      const remain =  gentime + lifetime - now
      return remain>0 && remain + '秒' || '过期'
    }
  }
};
</script>
<style lang="less">
.setting-nav {
  font-size: 14px;
}
.side-content {
  .header {
    font-size: 16px;
    padding-left: 10px;
    padding-top: 5px;
  }
}
.nav-filter {
  padding: 0 10px;
}
// .el-tabs__item {
//   padding: 0 20px !important;
// }
// .el-tabs__active-bar{
//   display: none !important;
// }
</style>