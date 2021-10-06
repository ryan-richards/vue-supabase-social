<template>
<n-layout>
<n-layout-header align="left">
    <n-space justify="space-between" >
<n-h2 align="left">Social Feed</n-h2> 
<n-menu v-model:value="activeKey" mode="horizontal" :options="menuOptions" v-if="!(isMobile || isTablet)"/>

<n-dropdown
    v-if="isMobile || isTablet"
    trigger="click"
    :options="menuOptions"
    :show-arrow="true"
    placement="bottom-end"
  >
<n-button>
      <template #icon>
        <n-icon>
          <menu-outline />
        </n-icon>
      </template>
    </n-button>
</n-dropdown>

    </n-space>
</n-layout-header>
</n-layout>
</template>

<script>

import { defineComponent, h, ref } from 'vue'
import { NIcon } from 'naive-ui'
import { RouterLink } from 'vue-router'
import {
  MenuOutline,
  DesktopOutline as WorkIcon,
  LogOutOutline as HomeIcon
} from '@vicons/ionicons5'
import { useIsMobile, useIsTablet } from '../utils/composables'

function renderIcon (icon) {
  return () => h(NIcon, null, { default: () => h(icon) })
}

const menuOptions = [
  {
    label: () =>
      h(
        RouterLink,
        {
          to: {
            name: 'Profile',
          }
        },
        { default: () => 'Go Home' }
      ),
    key: 'go-back-home',
    icon: renderIcon(HomeIcon)
  },
  {
    label: () =>
      h(
        RouterLink,
        {
          to: {
            path: '/feed'
          }
        },
        { default: () => 'Feed' }
      ),
    key: 'feed',
    icon: renderIcon(WorkIcon)
  }
]

export default defineComponent({
components: {
   MenuOutline
  },
  setup () {


    const isMobileRef = useIsMobile()
    const isTabletRef = useIsTablet()




    return {
      overlap: ref(false),
      activeKey: ref(null),
      isMobile: isMobileRef,
      isTablet: isTabletRef,
      menuOptions
    }
  }
})

</script>


<style scoped>

.n-layout-header,
.n-layout-footer {
  background: rgb(15, 15, 15);
  padding: 24px;
}

</style>