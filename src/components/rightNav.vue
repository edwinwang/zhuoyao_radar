<template>
  <div class="setting-nav">
    <div class="burger-wrap">
      <div :class="['nav-burger', showMenu ? 'active' : '']">
        <div class="setting-icon" @click.prevent="openMenu">
          <i v-if="showMenu" class="el-icon-d-arrow-right"></i>
          <i v-else class="el-icon-setting"></i>
        </div>
      </div>
    </div>
    <transition name="black">
      <div
        class="black-screen"
        v-show="showMenu"
        @click.prevent="openMenu"
      ></div>
    </transition>
    <transition name="side">
      <div class="side-nav" v-show="showMenu">
        <div class="side-header">
          <h4>捉妖雷达 - Web</h4>
          <p>{{ version }}</p>
          <!-- <p><a href="http://note.youdao.com/noteshare?id=cf93745c828b381275f53e5a730eaf96" target="_blank">捉妖雷达开发贡献指引</a></p> -->
          <br>
          <!-- <p>欢迎使用虚拟定位</p> -->
          <br/>
        </div>
        <div class="side-content">
          <div class="nav-filter">
            <el-collapse v-model="activeName" accordion>
              <el-collapse-item :title="'筛选'+(settings.use_custom?'(已启用自定义)':'')" name="1">
                <ul v-if="mode === 'normal' || mode === 'temp' ">
                  <template v-for="item in filters">
                    <li :key="item.key">
                      <span class="tag">{{item.text}}</span>
                      <el-switch v-model="settings.fit[item.key]" :disabled="settings.use_custom"> </el-switch>
                    </li>
                  </template>
                </ul>
                <ul v-else>
                  <template v-for="item in settings.wide">
                    <li :key="item.id">
                      <span class="tag">{{item.name}}</span>
                      <el-switch v-model="item.on" :disabled="settings.use_custom"> </el-switch>
                    </li>
                  </template>
                </ul>
              </el-collapse-item>
              <el-collapse-item title="设置" name="2">
                <ul>
                  <li>
                    <span class="tag">启用自定义筛选</span>
                    <el-switch v-model="settings.use_custom"> </el-switch>
                  </li>
                  <li>
                    <span class="tag">点击地图自动搜索</span>
                    <el-switch v-model="settings.auto_search"> </el-switch>
                  </li>
                  <li>
                    <span class="tag">显示剩余时间</span>
                    <el-switch v-model="settings.show_time"> </el-switch>
                  </li>
                  <li>
                    <span class="tag">记住上次退出位置</span>
                    <el-switch v-model="settings.position_sync"> </el-switch>
                  </li>
                  <li>
                    <span class="tag">显示搜索范围</span>
                    <el-switch v-model="settings.show_box"> </el-switch>
                  </li>
                </ul>
              </el-collapse-item>
            </el-collapse>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>
<script>
import { getLocalStorage } from '../lib/util';

export default {
  name: 'radar-right-nav',
  props: {
    version: {
      type: String,
      default: ''
    },
    settings: {
      type: Object,
      default: {}
    },
    showMenu: {
      type: Boolean,
      default: false
    },
    mode: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      activeName: '1',
      filters: [
        {
          text: '稀有',
          key: 'rare'
        },
        {
          text: '1觉',
          key: 't1'
        },
        {
          text: '2觉',
          key: 't2'
        },
        {
          text: '巢穴',
          key: 'nest'
        },
        {
          text: '地域',
          key: 'feature'
        },
        {
          text: '鬼鬼',
          key: 'ghost'
        },
        {
          text: '鲲鲲',
          key: 'fish'
        },
        {
          text: '元素',
          key: 'element'
        },
        {
          text: '其他所有（慎选）',
          key: 'all'
        },
      ]
    };
  },
  methods: {
    openMenu() {
      this.$emit('update:showMenu', !this.showMenu);
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
.nav-filter{
  padding: 0 10px;
}
// .el-tabs__item {
//   padding: 0 20px !important;
// }
// .el-tabs__active-bar{
//   display: none !important;
// }
</style>