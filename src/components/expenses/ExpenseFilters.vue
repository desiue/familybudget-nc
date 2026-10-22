<template>
  <div class="filters">
    <div class="filter-item search-filter">
      <NcTextField
        v-model="local.query"
        label="Suche"
        placeholder="Beschreibung durchsuchen"
        @input="emit" />
    </div>
    <div class="filter-item user-filter">
      <NcSelect
        v-model="local.user"
        :options="userOptions"
        input-label="Benutzer filtern"
        placeholder="Nach Benutzer filtern"
        :clearable="true"
        :append-to-body="true"
        @input="onUserChange" />
    </div>
    <div class="filter-item date-filter">
      <NcTextField
        v-model="local.from"
        type="date"
        label="Von"
        @input="emit" />
    </div>
    <div class="filter-item date-filter">
      <NcTextField
        v-model="local.to"
        type="date"
        label="Bis"
        @input="emit" />
    </div>
    <div class="filter-item button-filter">
      <NcButton
        variant="tertiary"
        @click="reset"
        aria-label="Filter zurücksetzen">
        Zurücksetzen
      </NcButton>
    </div>
  </div>
</template>

<script>
import NcTextField from '@nextcloud/vue/dist/Components/NcTextField.js'
import NcSelect from '@nextcloud/vue/dist/Components/NcSelect.js'
import NcButton from '@nextcloud/vue/dist/Components/NcButton.js'

export default {
  name: 'ExpenseFilters',
  components: { NcTextField, NcSelect, NcButton },
  props: {
    value: { type: Object, required: true },
    expenses: { type: Array, default: () => [] },
  },
  data() {
    return {
      local: { query: '', user: null, from: '', to: '' },
    }
  },
  computed: {
    userOptions() {
      const set = new Set(this.expenses.map(e => e.user_uid).filter(Boolean))
      return Array.from(set).sort()
    },
  },
  watch: {
    value: {
      deep: true,
      immediate: true,
      handler(v) {
        this.local = { query: '', user: null, from: '', to: '', ...(v || {}) }
      },
    },
  },
  methods: {
    onUserChange(val) {
      this.local.user = val || null
      this.emit()
    },
    emit() {
      this.$emit('input', { ...this.local })
    },
    reset() {
      this.local = { query: '', user: null, from: '', to: '' }
      this.emit()
    },
  },
}
</script>

<style scoped>
.filters {
  display: grid;
  grid-template-columns: 2fr 1.5fr 1fr 1fr auto;
  gap: 16px;
  align-items: end; /* Zwingt alle Elemente, unten sauber abzuschließen */
  max-width: 100%;
  margin: 16px 0;
  padding: 0;
  position: relative;
  z-index: 50;
}

.filter-item {
  min-width: 0; /* Verhindert das Sprengen des Grids durch Flex/Inputs */
}

.button-filter {
  display: flex;
  align-items: center;
  padding-bottom: 2px; /* Zieht den Button perfekt auf Input-Höhe */
}

/* Exakte Anpassung des NcSelect an Nextcloud Standard-Inputs */
.user-filter :deep(.multiselect) {
  min-height: 44px;
}

.user-filter :deep(.multiselect__tags) {
  min-height: 44px;
  max-height: 44px;
  border-radius: var(--border-radius, 8px);
  border: 1px solid var(--color-border);
  background-color: var(--color-main-background);
  display: flex;
  align-items: center;
  padding: 0 40px 0 12px;
}

.user-filter :deep(.multiselect__single),
.user-filter :deep(.multiselect__input) {
  background: transparent !important;
  margin-bottom: 0 !important;
  line-height: normal;
  padding-top: 0 !important;
}

.user-filter :deep(.multiselect__select) {
  height: 44px;
}

/* Einheitliches Label-Styling erzwingen, damit das Suchfeld nicht verrutscht */
:deep(.input-field__label),
.user-filter :deep(.multiselect__label),
.user-filter :deep(label) {
  display: block;
  height: 20px; /* Feste Texthöhe oben für alle Labels reservieren */
  margin-bottom: 4px;
  font-size: var(--font-size-small, 13px);
  color: var(--color-text-maxcontrast);
  white-space: nowrap;
}

@media (max-width: 1024px) {
  .filters {
    grid-template-columns: 1fr 1fr;
  }
  .button-filter {
    grid-column: span 2;
    justify-content: flex-start;
  }
}

@media (max-width: 600px) {
  .filters {
    grid-template-columns: 1fr;
  }
  .button-filter {
    grid-column: span 1;
  }
}
</style>