<template>
  <div class="edit">
    <h1>TODOLIST</h1>
    <label v-for="label in options">
      <input type="radio"
      v-model="current"
      v-bind:value="label.value">{{ label.label }}</input>
    </label>
    <table>
      <thead v-pre>
        <tr>
          <th class="todo">TODO</th>
          <th class="state">状態</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in computedTodos" :key="item.id" v-bind:class="{done:item.state}">
          <td>{{ item.comment }}</td>
          <td class="state">
            <button v-on:click="doChangeState(item)">
              {{ labels[item.state] }}
            </button>
          </td>
          <td class="button">
            <button v-on:click="doRemove(item)">
              削除
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <h2>新しい作業の追加</h2>
    <form action="" class="add-form" v-on:submit.prevent="doAdd">
      作業内容 <input type="text" ref="comment">
      <button type="submit">追加</button>
    </form>
  </div>
</template>

<script>

let STORAGE_KEY = 'todos-vuejs-demo'
let todoStorage = {
  fetch: function() {
    let todos = JSON.parse(
      localStorage.getItem(STORAGE_KEY) || '[]'
    )
    todos.forEach(function(todo, index) {
      todo.id = index
    })
    todoStorage.uid = todos.length
    return todos
  },
  save: function(todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}

export default {
  name: 'edit',
  data: () => {
    return {
      todos: [],
      options: [
        { value: -1, label: 'すべて'},
        { value: 0, label: '作業中'},
        { value: 1, label: '完了'},
      ],
      current: -1
    }
  },
  methods: {
    doAdd: function(event, value) {
      var comment = this.$refs.comment
      if (!comment.value.length) {
        return
      }
      this.todos.push({
        id: todoStorage.uid++,
        comment: comment.value,
        state: 0
      })
      comment.value = ''
    },
    doChangeState: function(item) {
      item.state = item.state ? 0 : 1
    },
    doRemove: function(item) {
      var index = this.todos.indexOf(item)
      this.todos.splice(index, 1)
    }
  },
  watch: {
    todos: {
      handler: function(todos) {
        todoStorage.save(todos)
      },
      deep: true
    }
  },
  created() {
    this.todos = todoStorage.fetch()
  },
  computed: {
    computedTodos: function() {
      return this.todos.filter(function(el) {
        return this.current < 0 ? true : this.current === el.state
      }, this)
    },
      labels: function() {
        return this.options.reduce(function(a, b) {
          return Object.assign(a, { [b.value]: b.label })
        }, {})
      }
    }
  }
</script>

<style media="screen">
  table {
    margin: 0 auto;
  }

  .done {
    text-decoration: line-through;
  }
</style>
