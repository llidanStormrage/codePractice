<template>
  <a-layout style="min-height: 100vh">
    <a-layout-header class="header">
      <!--todo: 头部组件-->
      <!-- <div class="logo" /> -->
      <Header />
    </a-layout-header>
    <!--todo: 面包屑待做，通过面包屑进行页面的跳转-->
    <a-layout-content style="padding: 0 50px">
      <Breadcrumb/>
      <a-layout style="padding: 24px 0; background: #fff">
        <a-layout-sider width="200" style="background: #fff">
          <!--通过递归子组件方式进行渲染
              注意里面的数据结构一定要是一样的，
              否则无法进行递归-->
          <a-menu v-model:selectedKeys="selectedKeys" v-model:openKeys="openKeys" mode="inline" style="height: 100%"
            @click="getData" @select="setData" @deselect="cancelSelect">
            <template v-for="item in menuList" :key="item.key">
              <template v-if="!item.children">
                <a-menu-item :key="item.key">
                  <template #icon>
                    <component :is="$antIcons[item.icon]" />
                  </template>
                  {{ item.title }}
                </a-menu-item>
              </template>
              <template v-else>
                <sub-menu :key="item.key" :menu-info="item" ref="childComponent" />
              </template>
            </template>
          </a-menu>
        </a-layout-sider>
        <a-layout-content :style="{ padding: '0 24px', height: '100%', width: '100%' }">
          <!--todo: 路由跳转-->
          <router-view />
        </a-layout-content>
      </a-layout>
    </a-layout-content>
    <!--todo：footer组件-->
    <a-layout-footer style="text-align: center">
      <Footer />
    </a-layout-footer>
  </a-layout>
</template>
<script lang="ts" setup>
import Header from '@/components/header/header.vue'
import Breadcrumb from '@/components/breadcrumb/index.vue'
import Footer from '@/components/footer/footer.vue'
import SubMenu from '@/components/aside/subMenu.vue'
import menuList from '@/mock/menuList'
import { ref, getCurrentInstance, onMounted, onBeforeUnmount } from 'vue'
import useStore from '@/store/index'
import { useRouter } from 'vue-router'

const proxy = getCurrentInstance()?.proxy
const $antIcons = proxy?.$antIcons
const collapsed = ref(false)
const selectedKeys = ref<string[]>(['']) // 父tab的key
const openKeys = ref<string[]>(['']) // 子tab下的展开
const { menu } = useStore()
const childComponent: any = ref(null)
const router = useRouter()
onMounted(() => {
  selectedKeys.value = menu.selectedKeys
  openKeys.value = menu.openKeys
})
const getData = () => {
  menu.setOpenKeys(openKeys.value)
  /**
   * 有个坑，router.currentRoute.value.path在使用的时候要考虑异步的问题：想获取当前路由，但是在a路由跳转b路由的时候，a路由的地址会被先记录下来，因为还没有来得及跳转完成被记录；
   * 所以为了解决此问题，使用pinia记录的时候需要延迟1s
   */
  setTimeout(() => {
    menu.setRouterPath(router.currentRoute.value.path)
  },1000)
  // console.log('asdadasdasdasdadasdasdasd',router,openKeys.value)
}
const setData = (data: any) => {
  // console.log('12313123', data, router.currentRoute.value.fullPath)
  menu.setSelectKeys(selectedKeys.value) 
}
const cancelSelect = () => {
  console.log('asdadasdasdasdadasdasdasd')
}
onBeforeUnmount(() => {
  menu.selectedKeys = ['']
  menu.openKeys = ['']
})

</script>

<style lang="less" scoped>
#components-layout-demo-top-side .logo {
  float: left;
  width: 120px;
  height: 31px;
  margin: 16px 24px 16px 0;
  background: rgba(255, 255, 255, 0.3);
}

.ant-row-rtl #components-layout-demo-top-side .logo {
  float: right;
  margin: 16px 0 16px 24px;
}

.site-layout-background {
  background: #fff;
}

.header {
  width: 100vw;
  height: 10vh;
}
</style>
