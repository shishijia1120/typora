# Vant

## 1 组件

### 1.1 Layout布局

- 引入

  ```vue
  import Vue from 'vue';
  import { Col, Row } from 'vant';
  
  Vue.use(Col);
  Vue.use(Row);
  ```

#### 1 代码演示

Layout 组件提供了 `24列栅格`，通过在 `Col` 上添加 `span` 属性设置列所占的宽度百分比。此外，添加 `offset` 属性可以设置列的偏移宽度，计算方式与 span 相同。

#### 2 API

- Row Props

| 参数   | 说明                          | 类型               | 默认值 |
| ------ | ----------------------------- | ------------------ | ------ |
| type   | 布局方式，可选值为`flex`      |                    |        |
| gutter | 列元素之间的间距（单位为 px） | *number \| string* |        |

- Col Props

| 参数   | 说明           | 类型               | 默认值 |
| ------ | -------------- | ------------------ | ------ |
| span   | 列元素宽度     | *number \| string* |        |
| offset | 列元素偏移距离 | *number \| string* |        |
| tag    |                |                    |        |

- Row Events

| 事件名 | 说明       | 回调参数    |
| ------ | ---------- | ----------- |
| click  | 点击时触发 | event:Event |

- Col Events

| 事件名 | 说明       | 回调参数    |
| ------ | ---------- | ----------- |
| click  | 点击时触发 | event:Event |

### 1.2 Toast轻提示

- 引入

```vue
import Vue from 'vue';
import { Toast } from 'vant';

Vue.use(Toast);
```

#### 1 代码演示

- 全局方法

```vue
export default{
mounted(){
	this.$toast('提示文案');
}
}
```

#### 2 API

| 方法名        | 说明 | 参数               | 返回值 |
| ------------- | ---- | ------------------ | ------ |
| Toast         |      | options \| message |        |
| Toast.loading |      | options \| message |        |
| Toast.success |      | options \| message |        |
| Toast.fail    |      | options \| message |        |
| Toast.clear   |      | options \| message |        |

## 2 表单组件







## 3 反馈组件



## 4 展示组件



## 5 导航组件

### 1 Tabbar标签栏



#### 1.1 代码演示

```vue
<template>
    <div>
        <div class="main-div">
         <router-view/>
        </div>

        <van-tabbar v-model="active" @change="changeTabbar(active)">
            <van-tabbar-item icon="shop">首页</van-tabbar-item>
            <van-tabbar-item icon="records">列表页</van-tabbar-item>
            <van-tabbar-item icon="cart">购物车页</van-tabbar-item>
            <van-tabbar-item icon="contact" info="20">会员中心</van-tabbar-item>
        </van-tabbar>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                active: 0
            }
        },
        methods: {
            changeTabbar(active) {
                console.log(active)
                switch(active){
                    case 0:
                        this.$router.push({name:'ShoppingMall'})
                        break;
                    case 1:
                        this.$router.push({name:'CategoryList'})
                        break;
                    case 2:
                        this.$router.push({name:'Cart'})
                        break;
                    case 3:
                        break;
                }
            }
        },
    }
</script>
```



#### 1.2 API

- Tabbar Props

| 参数    | 说明                       | 类型           | 默认值 |
| ------- | -------------------------- | -------------- | ------ |
| v-model | 当前选中标签的名称或索引值 | number\|string | 0      |



- Tabbar  Events

| 事件名 | 说明           | 回调参数                           |
| ------ | -------------- | ---------------------------------- |
| change | 切换标签时触发 | active: 当前选中标签的名称或索引值 |

- TabbarItem Props

| 参数    | 说明                                                 | 参数            | 默认值           |
| ------- | ---------------------------------------------------- | --------------- | ---------------- |
| name    | 标签名称                                             | nnumber\|string | 当前标签的索引值 |
| icon    | 图标名称                                             |                 |                  |
| dot     | 是否显示图标右上角小红点                             |                 |                  |
| badge   | 右上角徽标的内容                                     |                 |                  |
| url     | 点击后跳转的链接地址                                 |                 |                  |
| to      | 点击后跳转的目标路由对象，同 vue-router 的 [to 属性] |                 |                  |
| replace |                                                      |                 |                  |



## 6 业务组件

### 6.1 商品卡片

- 引入

```vue
import Vue from 'vue';
import { Card } from 'vant';

Vue.use(Card);
```

#### 1 代码演示

```vue
<van-card
  num="2"
  price="2.00"
  desc="描述信息"
  title="商品标题"
  thumb="https://img01.yzcdn.cn/vant/ipad.jpeg"
>
  <template #tags>
    <van-tag plain type="danger">标签</van-tag>
    <van-tag plain type="danger">标签</van-tag>
  </template>
  <template #footer>
    <van-button size="mini">按钮</van-button>
    <van-button size="mini">按钮</van-button>
  </template>
</van-card>
```

#### 2 API

| 参数         | 说明 | 类型 | 默认值 |
| ------------ | ---- | ---- | ------ |
| thumb        |      |      |        |
| title        |      |      |        |
| desc         |      |      |        |
| tag          |      |      |        |
| num          |      |      |        |
| price        |      |      |        |
| origin-price |      |      |        |

- Events

| 事件名      | 说明                 | 回调参数 |
| ----------- | -------------------- | -------- |
| click       | 点击时触发           |          |
| click-thumb | 点击自定义图片时触发 |          |



- slots





### 6.2 提交订单栏



