<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { components } from '@/editor-components/index'
import type { ComponentDef } from '@/editor-components/index'

// 用于给每个组件配一个插入模板（覆盖自带的 example，更接地气）
const INSERT_TEMPLATES: Record<string, string> = {
  Badges_DA01: '<badges tone="accent">标签1|标签2|标签3</badges>',
  Breaking_DA01: '<breaking badge="NEW" title="标题" subtitle="副标题" chips="标签1|标签2">\n正文内容段落\n</breaking>',
  CaseFlow_DA01:
    '<case-flow>\n- [案例 1] 标题|描述\n- [案例 2] 标题|描述\n</case-flow>',
  Compare_DA01:
    '<compare left-label="之前" left-title="旧版" right-label="之后" right-title="新版">\n<left>\n旧版内容\n</left>\n<right>\n新版内容\n</right>\n</compare>',
  Compare_DA02:
    '<compare type="DA02" left-label="之前" left-title="旧版" right-label="之后" right-title="新版">\n<left>旧版内容</left>\n<right>新版内容</right>\n</compare>',
  CTA_DA01: '<cta label="GET STARTED" title="标题文字" button="按钮文字"></cta>',
  Engage_DA01:
    '<engage title="如果这篇文章对你有帮助，欢迎点赞、转发！" label="THANKS FOR READING"></engage>',
  Engage_DA02:
    '<engage type="DA02" title="感谢你的阅读与支持！" subtitle="喜欢就互动一下吧～"></engage>',
  Img_DA01:
    '<img src="图片URL" width="100%" height="auto" radius="8px" fit="cover" align="left" />',
  Lead_DA01: '<lead>引导文字段落</lead>',
  PTitle_DA01:
    '<p-title num="01" title="段落标题" subtitle="副标题" level="1"></p-title>',
  ReadingPath_DA01: '<reading-path></reading-path>',
  Slider_DA01:
    '<slider images="图片URL1,图片URL2,图片URL3" interval="3" type="1"></slider>',
  Statement_DA01: '<statement>居中强调文字</statement>',
  Steps_DA01:
    '<steps label="HOW IT WORKS" title="标题" active="1">\n- 步骤一|说明\n- 步骤二|说明\n</steps>',
  Steps_DA02:
    '<steps type="DA02" label="VERTICAL STEPS" title="标题" active="1">\n- 步骤一|说明\n- 步骤二|说明\n</steps>',
  Timeline_DA01:
    '<timeline>\n- 2024年1月|事件标题|描述\n- 2024年6月|事件标题|描述\n</timeline>',
  Title_DA01:
    '<title badge="标签" subtitle="副标题文字" chips="标签1|标签2">标题文字</title>',
  Title_DA02:
    '<title type="DA02" badge="标签" subtitle="副标题文字" chips="标签1|标签2">标题文字</title>',
}

interface Item {
  id: string
  name: string
  template: string
}

const items: Item[] = components.map((c: ComponentDef) => ({
  id: c.id,
  name: c.name,
  template: INSERT_TEMPLATES[c.id] || c.example || `<${c.tag}>内容</${c.tag}>`,
}))

const emit = defineEmits<{
  insert: [text: string]
}>()

const visible = ref(false)
const popoverClass = 'ci-popover'

function toggle() {
  visible.value = !visible.value
}

function onItemClick(item: Item) {
  emit('insert', item.template)
  visible.value = false
}

function onClickOutside(e: MouseEvent) {
  const target = e.target as HTMLElement
  if (!target.closest(`.${popoverClass}`)) {
    visible.value = false
  }
}

onMounted(() => document.addEventListener('click', onClickOutside))
onBeforeUnmount(() => document.removeEventListener('click', onClickOutside))
</script>

<template>
  <div :class="[popoverClass, 'relative inline-flex']">
    <button
      class="inline-flex items-center gap-1 px-2.5 rounded-[5px] bg-transparent text-[11px] font-medium cursor-pointer transition-all duration-150 whitespace-nowrap panel-action-btn"
      @click.stop="toggle"
    >
      插入组件
      <svg class="w-3 h-3 fill-none stroke-current stroke-2" viewBox="0 0 24 24">
        <polyline points="6 9 12 15 18 9" />
      </svg>
    </button>

    <!-- 弹出面板 -->
    <div
      v-if="visible"
      class="ci-panel absolute left-0 top-full mt-2 p-3 bg-white dark:bg-[#2a2a2e] rounded-xl z-50 w-72 max-h-80 overflow-y-auto"
      :style="{ boxShadow: '0 8px 32px rgba(0,0,0,0.12)' }"
    >
      <div class="grid grid-cols-3 gap-1.5">
        <button
          v-for="item in items"
          :key="item.id"
          class="text-left px-2.5 py-2 rounded-lg border-none bg-transparent cursor-pointer text-[12px] font-medium transition-colors duration-150 text-black/75 dark:text-white/75 hover:bg-black/5 dark:hover:bg-white/10"
          @click="onItemClick(item)"
        >
          {{ item.name }}
        </button>
      </div>
    </div>
  </div>
</template>
