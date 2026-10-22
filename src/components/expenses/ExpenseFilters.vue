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
        :aria-label-combobox="'Benutzer filtern'"
        placeholder="Benutzer"
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
/* Filterleiste: gleiche Breite wie Titel und Liste (max. 900px, zentriert), bricht von selbst um */
.filters {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  align-items: flex-end;
  max-width: 900px;
  margin: 8px auto 12px;
  padding: 0 8px;
}

.filter-item { min-width: 0; box-sizing: border-box; }
.search-filter { flex: 2 1 220px; }
.user-filter { flex: 1.5 1 180px; }
.date-filter { flex: 1 1 140px; }
.button-filter { flex: 0 0 auto; display: flex; align-items: center; }

/* Eingabefelder: Innenabstand in die Breite einrechnen, sonst ragen sie über den Rand (iPhone: Seite seitlich verschiebbar) */
.filters :deep(input) { box-sizing: border-box; max-width: 100%; }

/* NcSelect bringt eine eigene Mindestbreite mit, die die Spalte sprengt */
.user-filter :deep(.v-select),
.user-filter :deep(.select) {
  min-width: 0 !important;
  width: 100%;
  margin: 0 0 1px 0 !important; /* gleiche Unterkante wie die Textfelder */
}

/* Benutzerfeld (NcSelect, alter Rahmenstil) optisch an die Textfelder (neuer Stil) angleichen:
   kein echter Rahmen, sondern Linie oben + blasser Ring, gleiche Rundung und Höhe */
.user-filter :deep(.vs__dropdown-toggle) {
  border: none !important;
  border-radius: var(--border-radius-element, 8px) !important;
  box-shadow: 0 -1px var(--color-border-maxcontrast), 0 0 0 1px color-mix(in srgb, var(--color-border-maxcontrast), 65% transparent);
  min-height: var(--default-clickable-area, 34px);
  padding-bottom: 0;
}
.user-filter :deep(.v-select:hover .vs__dropdown-toggle),
.user-filter :deep(.v-select:focus-within .vs__dropdown-toggle),
.user-filter :deep(.vs--open .vs__dropdown-toggle) {
  box-shadow: 0 0 0 2px var(--color-border-maxcontrast);
}

/* iOS Safari: leeres Datumsfeld klappt sonst auf Höhe 0 zusammen */
.date-filter :deep(input[type="date"]) {
  min-height: var(--default-clickable-area, 34px);
  width: 100%;
  display: block;
  -webkit-appearance: none;
  appearance: none;
}

/* Handy: alles untereinander, volle Breite */
@media (max-width: 600px) {
  .search-filter,
  .user-filter,
  .date-filter { flex: 1 1 100%; }
}
</style>