<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <div>Sort by <a href="#" v-on:click="sortByDate(true)">ascending</a> | <a href="#" v-on:click="sortByDate(false)">descending</a></div>

    <ul v-for="comment in comments" :key="comment['.key']">
      <li class="comment">
          <span class="comment__head">
              {{ comment.displayName }}
              -
              {{ comment.createdAt }}
          </span>
          <span class="comment__message">
              {{ comment.message }}
          </span>
      </li>
    </ul>

    <h2>Leave message</h2>

    <form id="form" v-on:submit.prevent="addComment">
        <ul class="form">
            <li><input type="text" v-model="newComment.displayName" placeholder="Username"></li>
            <li><textarea v-model="newComment.message" placeholder="Message"></textarea></li>
            <li><input type="submit" value="Add Message"></li>
        </ul>
    </form>

    <ul class="errors">
      <li v-show="!validation.username">Username cannot be empty.</li>
      <li v-show="!validation.displayName">Username cannot be "admin".</li>
      <li v-show="!validation.message">Message cannot be empty.</li>
    </ul>
  </div>
</template>

<script>
import { getItem, setItem, removeItem } from '../store/storage'

export default {
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to Programming task',
      comments: [{
          displayName: 'Vitaly',
          message: 'My message',
          createdAt: new Date(2015, 0, 12)
      },{
          displayName: 'Vlad',
          message: 'My message',
          createdAt: new Date(2017, 0, 12)
      },{
          displayName: 'Olga',
          message: 'My message',
          createdAt: new Date(2016, 0, 12)
      }],
      newComment: {
        displayName: '',
        message: '',
        createdAt: new Date
      }
    }
  },

    computed: {
      validation: function () {
        return {
          displayName: !this.newComment.displayName.includes('admin'),
          username: !!this.newComment.displayName.trim(),
          message: !!this.newComment.message.trim()
        }
      },
      isValid: function () {
        var validation = this.validation
        return Object.keys(validation).every(function (key) {
          return validation[key]
        })
      }
    },

    methods: {
        sortByDate: function (ascending) {
          return this.comments.sort((a, b) => {
              var c = new Date(a.createdAt);
              var d = new Date(b.createdAt);
              if (ascending) {
                  return c - d;
              } else {
                  return d - c;
              }
            })
        },
        addComment: function () {
          if (this.isValid) {
              this.comments.push({
                  displayName: this.newComment.displayName,
                  message: this.newComment.message,
                  createdAt: this.newComment.createdAt
              })

              setItem('comments', this.comments)

              this.newComment.displayName = ''
              this.newComment.message = ''
          }
        }
    },

    created () {
        if (getItem('comments')) {
            this.comments = getItem('comments')
        }
    }
}
</script>

<style scoped>
.comment__head {
    display: flex;
}

.form {
    list-style: none;
    padding: 0;
}

.errors {
    color: red;
}
</style>
