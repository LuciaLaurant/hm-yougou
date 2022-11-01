<template>
  <view>
    <!-- 搜索栏 -->
    <my-search @inputHandler="getInputValue"></my-search>

    <!-- 搜索建议列表 -->
    <view class="sugg-list">
      <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>

    <!-- 搜索历史 -->
    <view class="history-box">
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item, i) in historyList" :key="i"></uni-tag>
      </view>
    </view>
  </view>
</template>

<script>
  import MySearch from '../../components/my-search.vue'
  export default {
    data() {
      return {
        // 搜索结果
        searchValue: '',
        // 定时器
        timer: null,
        // 搜索结果列表
        searchResults: [],
        // 历史列表
        historyList: ['a', 'app', 'apple'],
      }
    },
    components: {
      'my-search': MySearch
    },
    methods: {
      // 从子组件获取关键词
      getInputValue(value) {
        // 清除定时器
        clearTimeout(this.timer)
        this.timer = setTimeout(() => {
          this.searchValue = value
          this.getSearchList()
        }, 500)
      },

      // // 根据搜索关键词，搜索商品建议列表
      async getSearchList() {
        // 判断关键词是否为空
        if (this.searchValue === '') {
          this.searchResults = []
          return
        }

        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/qsearch', {
          query: this.searchValue
        })
        if (res.meta.status !== 200) return uni.$showMsg()
        this.searchResults = res.message

      },

      // 跳转到详情页
      gotoDetail(id) {
        uni.navigateTo({
          // 指定详情页面的 URL 地址，并传递 goods_id 参数
          url: '/subpkg/goods_detail/goods_detail?goods_id=' + id
        })
      },
    },
  }
</script>

<style lang="scss">
  .sugg-list {
    padding: 0 5px;

    .sugg-item {
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;
      display: flex;
      align-items: center;
      justify-content: space-between;

      .goods-name {
        // 文字不允许换行（单行文本）
        white-space: nowrap;
        // 溢出部分隐藏
        overflow: hidden;
        // 文本溢出后，使用 ... 代替
        text-overflow: ellipsis;
        margin-right: 3px;
      }
    }
  }

  .history-box {
    padding: 0 5px;

    .history-title {
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 40px;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
    }

    .history-list {
      display: flex;
      flex-wrap: wrap;

      .uni-tag {
        margin-top: 5px;
        margin-right: 5px;
      }
    }
  }
</style>
