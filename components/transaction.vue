<template>
  <div class="grid grid-cols-2 py-4 border-b">
    <div class="flex items-center justify-between">
      <div class="flex items-center space-x-1">
        <Icon :name="icon" :class="color" />
        <div>{{ transaction.description }}</div>
      </div>
      <div>
        <UBadge color="white" v-if="transaction.category">{{
          transaction.category
        }}</UBadge>
      </div>
    </div>
    <div class="flex items-center justify-end space-x-2">
      <div>{{ currency }}</div>
      <div>
        <UDropdown :items="items" :popper="{ placement: 'bottom-start' }">
          <UButton
            color="white"
            variant="ghost"
            trailing-icon="i-heroicons-ellipsis-horizontal"
            :loading="isLoading"
          />
        </UDropdown>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  transaction: Object,
});

const emit = defineEmits(["deleted"]);

const { currency } = useCurrency(props.transaction.amount);
const isIncome = computed(() =>
  props.transaction.type === "Income" ? true : false
);
const icon = computed(() =>
  isIncome.value ? "tdesign:arrow-right-down" : "tdesign:arrow-right-up"
);

const color = computed(() => (isIncome.value ? "green" : "red"));

const isLoading = ref(false);
const toast = useToast();
const supabase = useSupabaseClient();

const deleteTransaction = async () => {
  isLoading.value = true;
  try {
    await supabase.from("transactions").delete().eq("id", props.transaction.id);

    toast.add({
      title: "Transaction deleted",
      icon: "i-heroicons-check-circle",
      color: "green",
    });
    emit('deleted', props.transaction.id)
  } catch (error) {
    toast.add({
      title: "Transaction deleted",
      icon: "i-heroicons-exclamation-circle",
      color: "red",
    });
  } finally {
    isLoading.value = false;
  }
};

const items = [
  [
    {
      label: "Edit",
      icon: "i-heroicons-pencil-square-20-solid",
      click: () => console.log("Edit"),
    },
    {
      label: "Delete",
      icon: "i-heroicons-trash-20-solid",
      click: () => deleteTransaction(),
    },
  ],
];
</script>

<style scoped>
.green {
  @apply text-green-600 dark:text-green-400;
}

.red {
  @apply text-red-600 dark:text-red-400;
}
</style>
