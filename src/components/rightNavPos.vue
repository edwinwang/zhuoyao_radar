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
          <!-- :default-sort = "{prop: 'remain', order: 'ascending'}"> -->
          <el-table-column label="妖灵">
            <template slot-scope="scope">
              {{monsterName[scope.row.sprite_id] || scope.row.sprite_id}}
            </template>
          </el-table-column>
          <el-table-column label="坐标">
            <template slot-scope="scope">
              <el-button class="btnCopy" :type="scope.row.clicked && 'info' || 'primary'" :data-clipboard-text="posVal" @click="handleCopy('ios', scope.row, scope)">ios</el-button>
              <!-- <el-button class="btnCopy" :data-clipboard-text="posVal" @click="handleCopy('android', scope.row.longtitude, scope.row.latitude)">android</el-button> -->
            </template>
          </el-table-column>
          <el-table-column label="时间" prop="remain" :formatter="formatRemainTime"></el-table-column>
        </el-table>
      </div>
    </transition>
  </div>
</template>
<script>
import { getLocalStorage } from "../lib/util"
import MonsterConfig from "../lib/tempdata"
import clipboard from 'clipboard'
import { setInterval } from 'timers';
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
    setInterval(this.calcRemainTime, 2000)
  },
  methods: {
    openMenu() {
      this.$emit("update:showMenu", !this.showMenu);
    },
    calcRemainTime(){
      //console.log("test-----")
      for (var item of this.tableData){
        const now = Date.parse(new Date())/1000
        item.remain = item.gentime + item.lifetime - now
      }
      for (var i=this.tableData.length-1; i>=0; i--){
        let item = this.tableData[i]
        if (item.remain<=0){
          this.tableData.splice(i, 1)
        }
      }

    },
    getOffset(){
      console.log(this.$parent, this.$parent.lo === 'hz')
      if (this.$parent.lo === 'hz'){
        return [6456, 6533]
      } else if (this.$parent.lo === 'lasha'){
        return [9456, 6733]
      }
      return [6550, 5950]
    },
    handleCopy(tp, row, scope){
      //console.error(scope)
      const long = row.longtitude
      const la = row.latitude
      row.clicked = true
      const offset = this.getOffset()
      if (tp === 'ios'){
        this.posVal = (long+offset[0])/1000000 + ',' + (la+offset[1])/1000000
        //console.log(long+6456, la+6333, this.posVal)
        console.log(offset)
      } else {
        this.posVal = (la+2074)/1000000 + ',' + (long-4372)/1000000
      }
      // for (var i=0; i<this.tableData.length; i++){
      //   let item = this.tableData[i]
      //   if (item.id===row.id){
      //     this.tableData.splice(i, 1)
      //     break
      //   }
      // }
      //console.log(tp, long, la, this.posVal)
    },
    formatRemainTime(row, column, cellValue, index){
      return cellValue>0 && cellValue + '秒' || '过期'
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