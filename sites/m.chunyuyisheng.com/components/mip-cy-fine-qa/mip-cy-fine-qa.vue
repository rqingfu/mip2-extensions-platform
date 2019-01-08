<template>
  <div class="mip-cy-fine-qa">
    <div class="positive-tags">
      <span
        class="positive-list J-cy-mip-fine-qa cur"
        @click="changeTag('')">全部</span>
      <span
        v-for="(item, index) in hotConsults"
        :key="index"
        class="positive-list J-cy-mip-fine-qa"
        @click="changeTag(item.keywords)"
      >{{ item.keywords }}</span>
    </div>
    <div class="J-cy-mip-fine-qa-wrap">
      <div class="fine-qa-list">
        <div v-if="problemList.length">
          <a
            v-for="(item, index) in problemList"
            :key="index"
            :href="`/mip/qa/${item.id}/`"
            class="fine-qa"
          >
            <div class="fine-qa-title">
              <div class="fine-qa-ask-tag">问</div>
              <div>
                <div>{{ item.title }}</div>
                <p class="fine-qa-content">{{ item.ask }}</p>
              </div>
            </div>
          </a>
        </div>
        <div
          v-else
          class="no-content">没有相关内容</div>
      </div>
    </div>
  </div>
</template>

<style lang="less" scoped>
.mip-cy-fine-qa {
  .positive-tags {
    overflow: hidden;
    padding: 0.12rem 0.12rem 0;
    .positive-list {
      float: left;
      margin-right: 0.2rem;
      margin-bottom: 0.1rem;
      padding: 0.04rem 0.2rem;
      font-size: 0.14rem;
      color: #666;
      border-radius: 0.04rem;
      background-color: #f0f2f5;
      &.cur {
        color: #fff;
        background-color: #51b5c7;
      }
    }
  }

  .fine-qa-list {
    padding: 0 0.15rem;
  }

  .fine-qa {
    position: relative;
    display: block;
    padding: 0.2rem 0;
    font-family: PingFangSC-Regular;

    &::after {
      content: "";
      position: absolute;
      right: -0.15rem;
      bottom: 0;
      left: 0;
      border-bottom: 1px solid #f1f1f1;
    }
    &-title {
      display: flex;
      font-size: 0.15rem;

      flex-wrap: nowrap;
    }
    &-ask-tag {
      width: 0.16rem;
      height: 0.16rem;
      margin-top: 0.02rem;
      margin-right: 0.05rem;
      font-size: 0.13rem;
      line-height: 0.16rem;
      text-align: center;
      color: #fff;
      border-radius: 0.02rem;
      background: #ffa800;

      flex-grow: 0;
      flex-shrink: 0;
    }
    &-content {
      margin-top: 0.15rem;
      font-family: PingFangSC-Light;
      font-size: 0.14rem;
      letter-spacing: 0;
      color: #323232;
    }
  }

  .no-content {
    line-height: 1rem;
    text-align: center;
    color: #cecece;
  }
}
</style>

<script>
const domain = 'https://m.chunyuyisheng.com'
const positiveCaches = {}

export default {
  props: {
    doctorId: {
      type: String,
      default: ''
    },
    hotConsults: {
      type: Array,
      default: () => {
        return []
      }
    }
  },
  data () {
    return {
      problemList: []
    }
  },
  mounted () {
    // 初始化数据
    this.changeTag('')
  },
  methods: {
    changeTag (type) {
      const that = this
      const doctorId = that.doctorId
      const key = `${type}_type`
      if (positiveCaches[key]) {
        that.problemList = positiveCaches[key]
        return
      }
      fetch(`${domain}/m/doctor/${doctorId}/qa/?is_json=1&page_count=3&query=${type}`)
        .then((res) => { return res.json() })
        .then(function (res) {
          const problemList = (res && res.problem_list) || []
          that.problemList = positiveCaches[key] = problemList
        })
    }
  }
}
</script>
