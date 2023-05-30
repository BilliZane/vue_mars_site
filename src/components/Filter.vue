<template>
  <div class="filter">
    <div class="container">
      <div class="filter__button-wrap" v-show="showFilterBtn">
        <Button
          class="button"
          :text="
            showFilterBlock
              ? $t('filter.showFilters.hide')
              : $t('filter.showFilters.show')
          "
          @event="handleChangeFilter"
        >
          <BaseIcon iconName="menu" v-if="!showFilterBlock" />
          <BaseIcon iconName="close" v-else />
        </Button>
      </div>
      <div class="filter__wrap" v-show="showFilterBlock">
        <div
          class="filter__item"
          :class="{ 'filter__item--mt': showFilterBlock }"
        >
          {{ $t('filter.filterBy') }}
        </div>
        <form class="filter__item">
          <Datepicker
            v-model="calendarDataStore.date"
            :format="calendarDataStore.previewdDate"
            :clearable="false"
            :enableTimePicker="false"
            class="filter__item--mb filter__item--mr"
            :locale="calendarDataStore.calendarLocale"
            selectText="Выбрать"
            cancelText="Отмена"
          />

          <Select :items="marsImagesStore.camNames" v-if="marsImagesStore.imagesLength"/>
        </form>
        <div class="filter__item filter__item--mb-none">
          <Button :text="$t('filter.filterButton')" @click="loadPhotos">
            <BaseIcon iconName="find" />
          </Button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import { useMarsImages } from '../stores/marsImages'
import { useCalendarData } from '../stores/calendarData'
import Datepicker from '@vuepic/vue-datepicker'
import Button from './Button.vue'
import BaseIcon from './BaseIcon.vue'
import Select from './Select.vue'

export default {
  components: {
    Datepicker,
    Button,
    BaseIcon,
    Select
  },
  setup () {
    const showFilterBtn = ref(false)
    const showFilterBlock = ref(true)
    const windowWidth = ref(0)

    const marsImagesStore = useMarsImages()
    const calendarDataStore = useCalendarData()

    const updateWindowWidth = () => {
      windowWidth.value = window.innerWidth
      if (windowWidth.value <= 975) {
        showFilterBtn.value = true
        showFilterBlock.value = false
      } else {
        showFilterBtn.value = false
        showFilterBlock.value = true
      }
    }

    const handleChangeFilter = () => {
      showFilterBlock.value = !showFilterBlock.value
    }

    onMounted(() => {
      updateWindowWidth()
      window.addEventListener('resize', updateWindowWidth)
      const localeInStorage = localStorage.getItem('user-locale')
      if (localeInStorage) {
        calendarDataStore.calendarLocale = localeInStorage
      } else {
        return
      }
    })

    onUnmounted(() => {
      window.removeEventListener('resize', updateWindowWidth)
    })

    const loadPhotos = async () => {
      await marsImagesStore.load()
    }

    return {
      showFilterBtn,
      showFilterBlock,
      windowWidth,
      marsImagesStore,
      calendarDataStore,
      updateWindowWidth,
      handleChangeFilter,
      loadPhotos
    }
  }
}
</script>