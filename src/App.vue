<template>
  <div id="app" class="container">
    <h1>Title</h1>
    <h2>Essential Links</h2>
    <mk-table v-bind="getTableProps()">
      <template slot="name" slot-scope="props">
        <h4 v-html="props.rowData.name"></h4>
      </template>
    </mk-table>
  </div>
</template>

<script>
import MkTable from './components/MkTable'
import Moment from 'moment';

export default {
  name: 'App',
  components: {
      MkTable
  },
  methods: {
      getTableProps () {
          return {
              search: {enabled: true},
              items: [
                  {name: 'Maike', birthday: '1993-01-06'},
                  {name: 'Souza', birthday: '1992-01-06'},
                  {name: 'Nunes', birthday: '1994-01-06'},
                  {name: 'De', birthday: '1991-01-06'},
                  {name: 'MN', birthday: '1995-01-06'},
                  {name: 'MK', birthday: '1990-01-06'}
              ],
              fields: [
                  {
                      type: 'slot',
                      name: 'name',
                      title: 'name',
                      sort: true
                  },
                  {
                      name: 'birthday',
                      title: 'birth\'s date',
                  },
                  {
                      type: 'callback',
                      name: 'age',
                      title: 'age',
                      callback (item) {
                          return Moment(item.birthday, 'YYYY-MM-DD').fromNow(true);
                      },
                      sort: true
                  }
              ]
          }
      }
  }
}
</script>
