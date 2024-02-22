<template>
  <div>
    <div class="font-bold" :class="[color]">{{ title }}</div>
    <div class="text-2xl font-extrabold text-black dark:text-white mb-2">
      <USkeleton class="h-8 w-full" v-if="loading" />
      <div v-else>{{ currency }}</div>
    </div>
    <div>
      <USkeleton class="h-6 w-full" v-if="loading" />
      <div v-else class="flex space-x-1 items-center text-sm">
        <Icon :name="icon" class="w-6 h-6" :class="[color]" />
        <div class="text-gray-500 dark:text-gray-400">
          <span :class="color">{{ trendingPercentage }}</span> vs Last period
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  title: String,
  amount: Number,
  lastAmount: Number,
  loading: Boolean,
});
const { amount } = toRefs(props);
const trendingUpDown = computed(() => props.amount >= props.lastAmount);
const icon = computed(() =>
  trendingUpDown.value ? "tdesign:trending-up" : "tdesign:trending-down"
);
const color = computed(() => (trendingUpDown.value ? "green" : "red"));
const { currency } = useCurrency(amount);
const trendingPercentage = computed(() => {
  if (props.amount === props.lastAmount) return "~ %";

  const bigger = Math.max(props.amount, props.lastAmount);
  const smaller = Math.min(props.amount, props.lastAmount);

  const ratio = ((bigger - smaller) / bigger) * 100;

  return Math.ceil(ratio) + "%";
});
</script>

<style scoped>
.green {
  @apply text-green-600 dark:text-green-400;
}

.red {
  @apply text-red-600 dark:text-red-400;
}
</style>
