<template>
  <UiSelect v-model="category" :disabled="config.preview">
    <template v-slot:label>Category</template>
    <option
      v-for="(category, index) in categories"
      :key="index"
      :value="category"
    >
      {{ category }}
    </option>
  </UiSelect>
  <UiInput v-model="notes" :disabled="config.preview">
    <template v-slot:label>Notes</template>
  </UiInput>
</template>

<script>
import Plugin from '@snapshot-labs/snapshot.js/src/plugins/safeSnap';

export default {
  props: ['config', 'modelValue'],
  emits: ['update:modelValue'],
  data() {
    return {
      plugin: new Plugin(),
      categories: ['Grant', 'Salary', 'Expense'], // todo: make it dynamic
      notes: '',
      category: ''
    };
  },
  mounted() {
    if (
      this.modelValue &&
      Object.prototype.hasOwnProperty.call(this.modelValue, 'llama')
    ) {
      const { notes, category } = this.modelValue.llama;
      this.notes = notes;
      this.category = category;
    }
  },
  watch: {
    notes() {
      this.updateMetadata();
    },
    category() {
      this.updateMetadata();
    }
  },
  computed: {
    isValidCategory() {
      return this.categories.includes(this.category);
    }
  },
  methods: {
    updateMetadata() {
      if (this.config.preview) return;
      const modelValue = {
        llama: { notes: this.notes, category: this.category }
      };
      this.$emit('update:modelValue', modelValue);
    }
  }
};
</script>
