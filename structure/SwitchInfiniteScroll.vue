<template>
  <div v-show="isReady">
    <DiaryTab class="mt--30" :tabs="tabHeader.tabs" :curIdx="tabHeader.curIdx" @switchTab="switchTab"></DiaryTab>
    <!-- 参考首页精华日记 -->
    <div class="doctor-cases-wrapper"
      v-infinite-scroll="loadMore"
      infinite-scroll-disabled="diableLoad"
      infinite-scroll-distance="0">
      <DoctorCase
        v-show="caseList.length >= 1"
        v-for="diray in caseList"
        :key="diray.id"
        :diary="diray"
      ></DoctorCase>
      <!--这一部分与下面的MoreLoading的关系需要再处理下，逻辑没有清晰，需要考虑下是否与MoreLoading合并-->
      <div class="doctorycase-no-diaries" v-show="caseList.length === 0">
        暂无案例日记
      </div>
    </div>
    <MoreLoading v-show="!allLoaded" class="mt-20" :queryLoading="queryLoading" :allLoaded="allLoaded"></MoreLoading>
  </div>
</template>

<script>
/*
 * 该页面入口： 1.首页专家列表
 * 为了更加普遍化，可以将命名更具有代表一般的命名 -- 如_getCaselist修改为_getList
*/
import {IMG_PREFIX} from '@/common/config/config.js'
import { DOCTOR } from '@/models/index.js'
import DiaryTab from '@/components/diary/DiaryTab/DiaryTab'
import DoctorCase from '@/components/doctor/DoctorCase/DoctorCase'
import MoreLoading from '@/components/common/MoreLoading/MoreLoading'
import { MessageBox, Toast } from 'mint-ui'

export default {
  name: 'Doctor',
  components: {
    DiaryTab,
    DoctorCase,
    MoreLoading, // 滚动加载
    Loading, // 其它数据请求时加载
    MessageBox,
    Toast
  },
  data () {
    return {
      isLoading: false, // 是否正在加载数据
      tabHeader: {
        tabs: [
          '全部',
          '整形',
          '微整',
          '植发'
        ],
        curIdx: 0
      },
      tabType: null, // 类型切换后，后台传输值
      caseList: [], // 这个名称有必要参数化
      doctorId: '', // 医生id
      // 初始化滚动加载相关参数
      queryLoading: false,
      diableLoad: false, // 是过滤器终止无限滚动 触发
      allLoaded: false,
      totalNum: 0,
      pageNum: 1, // 默认值
      pageSize: 2 // 默认值
    }
  },
  methods: {
    init (self, id) {
      // 医生案例列表
      self._getCaselist(self, 'caseList', self.doctorId, self.pageNum, self.pageSize, self.tabType)
    },
    switchTab (idx) {
      let self = this
      // 这部分是否要写成配置呢？
      const idxMeaningTable = {
        '0': 'all',
        '1': 'shape', // 整形
        '2': 'small', // 微整
        '3': 'hair' // 植发
      }
      // 后台传输值
      const meaningTypeTable = {
        'all': '0', // 全部
        'shape': '1', // 整形
        'small': '4', // 微整
        'hair': '5' // 植发
      }
      this.tabHeader.curIdx = idx
      let tabType = this.tabType = meaningTypeTable[idxMeaningTable[idx]]
      // console.log('tabType', tabType)
      // 加载全部数据
      if (tabType === '0') {
        tabType = null
      }
      // 加载其它数据
      // 内容重置
      self.pageNum = 1
      self.caseList = []
      // 根据类型加载内容
      self._getCaselist(self, 'caseList', self.doctorId, self.pageNum, self.pageSize, tabType)
    },
    _getCaselist (self, receiverName, uid, pageNum, pageSize, category) {
      // 这里可以抽出成数据加载时请求函数
      self.queryLoading = true
      // 可以再函数参数化 to do
      DOCTOR.caseList(uid, pageNum, pageSize, category).then(res => {
        if (res.data.retCode === 0) {
          // 这里可不可以用哨兵呢？
          if (res.data.data.data.length === 0) {
            self.allLoaded = true
            self.diableLoad = true
            return
          }
          self[receiverName] = self[receiverName].concat(res.data.data.data)
          self[receiverName].forEach(item => {
            item.hasZan = false
          })
          self.queryLoading = false
          self.diableLoad = false
        } else if (res.data.retCode === 1) {
          // 没有数据
          self.queryLoading = false
          self.diableLoad = true
          self.allLoaded = true
        } else {
          MessageBox.alert(res.data.retMsg, '错误', {
            closeOnClickModal: true
          })
        }
      })
    },
    loadMore () {
      if (this.allLoaded) {
        this.diableLoad = true
        return
      }
      if (this.queryLoading) {
        return
      }
      this.pageNum++
      let self = this
      self._getCaselist(self, 'caseList', self.doctorId, self.pageNum, self.pageSize, self.tabType)
    }
  },
  created () {
    const self = this
    let id = this.$route.query.id
    self.doctorId = id
    self.isLoading = true
    self.init(self, id)
  }
}
</script>

<style>
.mt--30{
  margin-top: -.3rem;
}
.mt-20{
  margin-top: .2rem;
}
.doctor-cases-wrapper{
  padding-bottom: .2rem;
}
.doctor-cases-wrapper .case-wrapper{
  padding-bottom: .2rem;
}
.doctor-content-header-wrapper{
  padding: .38rem .2rem .24rem .2rem;
}
.doctor-fold-bg{
  background-color: #fff;
}
/* 没有日记的显示 */
.doctorycase-no-diaries{
  padding: .2rem 0;
  text-align: center;
}
</style>
