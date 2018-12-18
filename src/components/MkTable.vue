<template>
  <div class="table-responsive">
    <table class="table table-striped table-sm">
      <thead>
        <tr>
          <th v-for="(field, key) in fields" :key="key" v-html="field.title">
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, key) in items" :key="key">
          <td v-for="(field, key) in fields" :key="key">
            <template v-if="fieldIsSlot(field)">
              <slot :name="field.name" :row-data="item" :row-index="key"></slot>
            </template>
            <template v-else-if="fieldIsCallback(field)">
              <span v-html="field.callback(item)"></span>
            </template>
            <template v-else>
              <span v-html="item[field.name]"></span>
            </template>
          </td>
        </tr>
      </tbody>
      <tfoot>

      </tfoot>
    </table>
  </div>
</template>

<script>
export default {
  name: 'MkTable',
  props: {
      fields: {
          type: Array,
          default: () => []
      },
      items: {
          type: Array,
          default: () => []
      }
  },
  data () {
      return {

      }
  },
  methods: {
      fieldIs (field, type) {
          return (field.type || null) === type;
      },
      fieldIsSlot (field) {
          return this.fieldIs(field, 'slot');
      },
      fieldIsCallback (field) {
          return this.fieldIs(field, 'callback');
      }
  }
}
</script>
