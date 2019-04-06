<template>
  <div class="m-menu">
    <dl class="nav"
        @mouseleave="leave">
      <dt>全部分类</dt>
      <dd v-for="(item, index) in menu"
          :key="index"
          @mouseenter="enter">
        <i :class="item.type" />{{ item.name }}<span class="arrow" />
      </dd>
    </dl>
    <div v-if="kind"
         class="detail"
         @mouseenter="childEnter"
         @mouseleave="childLeave">
      <template v-for="(item, index) in curdetail.child">
        <h4 :key="index">{{ item.title }}</h4>
        <span v-for="v in item.child"
              :key="v">{{ v }}</span>
      </template>

    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      kind: '',
      menu: [
        {
          type: 'food',
          name: '美食',
          child: [
            {
              title: '美食',
              child: ['代金券', '甜点饮品', '火锅', '自助餐', '小吃快餐']
            }
          ]
        },
        {
          type: 'takeout',
          name: '外卖',
          child: [
            {
              title: '外卖',
              child: ['美团外卖']
            }
          ]
        },
        {
          type: 'hotel',
          name: '酒店',
          child: [
            {
              title: '酒店星级',
              child: ['经济型', '舒适/三星', '高档/四星', '豪华/五星']
            }
          ]
        }
      ]
    }
  },
  //计算属性被混入vue实例
  computed: {
    curdetail: function() {
      return this.menu.filter(item => item.type === this.kind)[0]
    }
  },
  //方法被混入vue实例
  methods: {
    //当指针离开元素，就会触发此事件
    leave: function() {
      let self = this
      self._timer = setTimeout(function() {
        self.kind = ''
      }, 200)
    },
    //当指针移动到元素，就会触发此事件
    enter: function(e) {
      this.kind = e.target.querySelector('i').className
    },
    //当指针离开子元素，就会触发此事件
    childLeave: function() {
      this.kind = ''
    },
    //当指针移动到子元素，就会触发此事件
    childEnter: function() {
      clearTimeout(this._timer)
    }
  }
}
</script>

<style>
</style>
