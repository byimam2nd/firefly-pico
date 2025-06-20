<template>
  <van-swipe-cell ref="swipeCell" v-bind="clickWithoutSwipe">
    <van-cell>
      <template #title>
        <div class="list-item-container" :class="itemClass">
          <div class="first_column flex-center flex-column">
            <!--            <app-icon :icon="icon ?? TablerIconConstants.budget" :size="TablerIconConstants.defaultSize" />-->
            <budget-icon :value="props.value" />
          </div>

          <div class="separator"></div>

          <div class="second_column flex-1 flex-column">
            <div v-if="displayName" class="title flex-center-vertical">
              <div class="flex-1">{{ displayName }}</div>
              <div class="text-size-12">{{ budgetLimitSpent }} / {{ budgetLimitAmount }} {{ budgetCurrencySymbol }}</div>
            </div>
            <!--            <bar-chart-item-horizontal :percent="budgetLimitPercent" class="p-0 mb-10" />-->
            <div class="display-flex flex-wrap gap-1 mt-5">
              <div class="tag-gray list-item-subtitle text-size-12">{{ budgetType }}</div>
              <div v-if="budgetAmount" class="tag-gray list-item-subtitle text-size-12">{{ budgetPeriod }}</div>
            </div>
          </div>
        </div>
      </template>
    </van-cell>

    <template #right>
      <van-button class="delete-button" square type="danger" text="Delete" @click="onDelete" />
    </template>
  </van-swipe-cell>
</template>

<script setup>
import _, { get } from 'lodash'
import { useClickWithoutSwipe } from '~/composables/useClickWithoutSwipe'
import Budget from '~/models/Budget.js'
import BudgetIcon from '~/components/budget/budget-icon.vue'

const props = defineProps({
  value: Object,
})

const emit = defineEmits(['onEdit', 'onDelete'])

const budgetLimit = computed(() => Budget.getLimit(props.value))

const displayName = computed(() => get(props.value, 'attributes.name', ' - '))
const budgetType = computed(() => get(props.value, 'attributes.auto_budget_type.name', ' - '))
const budgetPeriod = computed(() => get(props.value, 'attributes.auto_budget_period.name', ' - '))
const budgetAmount = computed(() => get(props.value, 'attributes.amount', ' - ') || ' - ')
const budgetLimitSpent = computed(() => Math.abs(get(budgetLimit.value, `attributes.spent`, 0)))
const budgetLimitAmount = computed(() => get(budgetLimit.value, 'attributes.amount') ?? 0)
const budgetCurrencySymbol = computed(() => Budget.getCurrencySymbol(props.value))

const itemClass = computed(() => ({
  'list-item-disabled': !get(props.value, 'attributes.active'),
}))

const onEdit = async (e) => {
  emit('onEdit', props.value)
}

const onDelete = async () => {
  emit('onDelete', props.value)
}

const swipeCell = ref(null)
const clickWithoutSwipe = useClickWithoutSwipe({ swipeCell: swipeCell, onClick: onEdit })
</script>

<style></style>
