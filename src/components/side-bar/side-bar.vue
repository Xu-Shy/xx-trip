<template>
  <div v-if="sideDatas.length" class="side-bar">
    <div class="side-menu">
      <van-sidebar v-model="currentSideActive" @change="onSideMenuChange">
        <template v-for="(item, index) in sideDatas" :key="index">
          <van-sidebar-item :title="item.label" />
        </template>
      </van-sidebar>
    </div>
    <div class="content">
      <slot
        name="content"
        :items="sideDatas[currentSideActive].items"
        :subGroups="sideDatas[currentSideActive].subGroups"
      >
        <template
          v-if="sideDatas[currentSideActive].items"
          v-for="(addrInfo, index) in sideDatas[currentSideActive].items"
          :key="index"
        >
          <div
            :class="['list-item', addrInfo.isSelect ? 'active' : '']"
            @click="handleItemClick(currentSideActive, index)"
          >
            <div class="name ellipsis-text-1">{{ addrInfo.label }}</div>
            <span class="desp" v-if="addrInfo.percentageUser">
              {{ addrInfo.percentageUser }}
            </span>
          </div>
        </template>

        <!-- sub menu -->
        <template v-if="getSubSide.length">
          <div class="sub-side-panel">
            <div class="sub-side-bar">
              <van-sidebar
                v-model="currentSubSideActive"
                @change="onSubSideMenuChange"
              >
                <template
                  v-for="(group, indy) in sideDatas[currentSideActive]
                    .subGroups"
                  :key="indy"
                >
                  <van-sidebar-item :title="group.label" />
                </template>
              </van-sidebar>
            </div>
            <div class="sub-content">
              <template v-for="(addr, indz) in getSubSideItems" :key="indz">
                <div
                  :class="(['list-item'], addr.isSelect ? 'active' : '')"
                  @click="handleSubItemClick(indz)"
                >
                  <div class="name ellipsis-text-1">{{ addr.label }}</div>
                  <span class="desp" v-if="addr.percentageUser">{{
                    addr.percentageUser
                  }}</span>
                </div>
              </template>
            </div>
          </div>
        </template>
      </slot>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watch } from "vue"

const props = defineProps({
  menuData: {
    type: Array,
    default: () => [],
  },
})

// side bar
const currentSideActive = ref(0)
const currentSubSideActive = ref(0)
const sideDatas = ref([])

watch(
  () => props.menuData,
  (newValue, oldValue) => {
    sideDatas.value = newValue
  },
  {
    immediate: true,
  }
)

const getSubSideItems = computed(() => {
  let subMenus = sideDatas.value[currentSideActive.value].subGroups || []
  let result = []
  if (subMenus.length) {
    result = subMenus[currentSubSideActive.value] || {}
  }
  result = result.items || []
  return result
})

const getSubSide = computed(() => {
  let subMenus = sideDatas.value[currentSideActive.value].subGroups || []
  return subMenus
})

const onSideMenuChange = () => {
  console.log(`${currentSideActive.value}`)
}
const onSubSideMenuChange = () => {
  console.log(`${currentSubSideActive.value}`)
}

const emit = defineEmits(["itemClick"])

const handleItemClick = (currentSideActive, index) => {
  if (sideDatas.value[currentSideActive]) {
    let addrInfo = sideDatas.value[currentSideActive].items || []
    addrInfo.forEach((item, i) => {
      item.isSelect = i === index
    })
    emit("itemClick", {
      sideDatas: sideDatas.value,
      currentSideActive,
      index,
    })
  }
}

const handleSubItemClick = (index) => {
  if (sideDatas.value[currentSideActive.value]) {
    let subGroup = sideDatas.value[currentSideActive.value].subGroup || []

    let addrInfos = subGroup[currentSubSideActive.value].item || []
    addrInfos.forEach((item, i) => {
      item.isSelect = i === index
    })
    emit("itemClick", {
      sideDatas: sideDatas.value,
      currentSideActive,
      currentSubSideActive,
      index,
    })
  }
}
</script>

<style lang="less" scoped>
@popupHeight: 500px;

.side-bar :deep(.van-sidebar) {
  height: calc(@popupHeight - 60px);
}

.side-bar {
  display: flex;
  flex-direction: row;
  .content {
    flex: 1;
    overflow-y: auto;
    height: calc(@popupHeight - 60px);
    background-color: white;

    .list-item {
      position: relative;
      display: flex;
      flex-direction: column;
      justify-content: center;
      height: 55px;

      padding: 0 20px 0 10px;
      margin-right: 20px;
      margin-left: 12px;

      font-size: 14px;

      .name {
        padding-bottom: 6px;
      }

      .desp {
        font-size: 12px;
        color: #999;
      }
    }
  }
}

.acitve {
  background-color: #fffcf5;
  .name {
    color: var(--primary-color);
  }
}

.sub-side-panel {
  display: flex;
  flex-direction: row;
  .sub-side-bar {
    border-right: 1px solid #f8f8f8;
  }
  .sub-side-bar :deep(.van-sidebar) {
    background-color: white !important;
  }
}

.sub-content {
  flex: 1;
  overflow-x: hidden;
  overflow-y: scroll;
  height: calc(@popupHeight - 60px);
}
</style>
