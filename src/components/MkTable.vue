<template>
  <div class="table-responsive">
    <table class="table table-striped table-sm">
      <thead>
        <tr>
          <th v-for="(field, key) in fields"
            v-bind:key="key"
            v-on:click="setSortField(field)">
            <span :class="{'font-italic': isFieldSortable(field)}" v-html="field.title"></span>
            <template v-if="isFieldSortable(field) && isFieldSortEnabled(field)">
              <img v-if="isFieldSortAsc()" src="./../assets/chevron-bottom.png"/>
              <img v-else src="./../assets/chevron-top.png"/>
            </template>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, key) in tableData"
            v-bind:key="key">
          <td v-for="(field, key) in fields"
            v-bind:key="key">
            <template v-if="isFieldSlot(field)">
              <slot v-bind:name="field.name"
                v-bind:row-data="item"
                v-bind:row-index="key">
              </slot>
            </template>
            <template v-else-if="isFieldCallback(field)">
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
          originalData: [],
          tableData: [],
          sort: {}
      };
  },
  mounted () {
      this.originalData = [...this.items];
      this.tableData = [...this.originalData];
  },
  methods: {
      isField (field, type) {
          return (field.type || null) === type;
      },
      isFieldSlot (field) {
          return this.isField(field, 'slot');
      },
      isFieldCallback (field) {
          return this.isField(field, 'callback');
      },
      isFieldSortable (field) {
          return field.sort || false
      },
      isFieldSortEnabled (field) {
          return (field.name || undefined) === (this.getSortField().name || null);
      },
      isFieldSortAsc () {
          return (this.sort.order || null) === 'asc';
      },
      setSortField (field) {
          if (!this.isFieldSortable(field)) {
              return;
          }

          if (!this.isFieldSortEnabled(field)) {
              this.sort = {
                  field: field,
                  order: 'asc'
              };
              return
          }

          if (this.isFieldSortAsc(field)) {
              this.sort = {
                  field: field,
                  order: 'desc'
              };
              return
          }

          this.sort = {};
      },
      sortData () {
          if (!this.getSortField().name || false) {
              this.tableData = [...this.originalData];
              return;
          }

          this.tableData.sort((a, b) => {
              let sortField = this.getSortField(),
                  comparison = 0,
                  varA = this.getColumnValue(sortField, a),
                  varB = this.getColumnValue(sortField, b);

              if (!varA || !varB) {
                  // property doesn't exist on either object
                  return false;
              }

              if (varA > varB) {
                  comparison = 1;
              } else if (varA < varB) {
                  comparison = -1;
              }

              return (this.sort.order == 'desc') ? (comparison * -1) : comparison;
          });
      },
      getColumnValue (field, record) {
          let value = null;
          switch (field.type || null) {
              case 'callback':
                  value = field.callback(record);
                  break;
              default:
                  value = record[field.name] || null;
          }

          return (typeof value === 'string') ? value.toUpperCase() : value;
      },
      getSortField () {
          return this.sort.field || {};
      }
  },
  watch: {
      sort () {
          this.sortData();
      }
  }
}
</script>
