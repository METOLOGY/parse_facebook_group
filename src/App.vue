<template>
  <div id="app">
     <div v-for="dataDay in dataRefine">
        <div class="post" v-for="post in dataDay">
            
            <span>{{ post.created_time }}</span>
            <span<strong>{{ post.from.name }}</strong></span> 
            <h3>{{ post.message }}</h3>
            <img class="preview-img" :src="img" v-for="img in post.ImagePath" />
            
            <a class="link" v-if="post.link" :href="post.link">{{ post.name }}</a>
            <p>讚： {{ post.likes.data.length }} </p>

            <div v-if="post.comments" v-for="(value, key) in post.comments">
            
              <div class="comment" v-if="key === 'data'">
                <h3>文章回應</h3>
                <p v-for="comment in value">
                  {{ comment.from.name }}: 
                  <span v-if="comment.message !== ''">{{ comment.message}}</span>
                  <span v-else>貼圖</span>
                </p>
              </div>
            </div>
            
            
        </div>
    </div>
  </div>
</template>

<script>
import $ from 'jquery'
import axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      dataDays: ''
    }
  },
  computed: {
    dataRefine () {
      const newData = this.dataDays

      console.log(newData)
      try {
        newData.forEach((Days) => {
          Days.forEach((Day) => {
            const val = Day.id
            const token = 'EAACEdEose0cBAN3pxm9HJlzVcZAjMOZBFh04KEUqZAGSr4c0fMoiyVHEcs7izGm54XaKugvzqhpzVImDsxFJfINnyfUcyiqlnxcxqzQQyfoKASdr08rvZBUVAm2qhpMYqnnEdmqn58p6fvesBg0cdbpkcq4nsxn7tYE32hU6M1HdLu9Hq2pR'
            const url = ` https://graph.facebook.com/v2.9/${val}/attachments?access_token=${token}`

            axios.get(url)
            .then((response) => {
              const raw = response.data.data[0]
              try {
                const images = raw.subattachments.data
                const imagePaths = []
                images.forEach((e) => {
                  imagePaths.push(e.media.image.src)
                })
                Day.ImagePath = imagePaths
              } catch (err) {
                Day.ImagePath = []
              }
            })
          })
        })
        return newData
      } catch (err) {
        console.log(err)
      }
    }
  },
  mounted () {
    const vm = this
    $.getJSON('/static/restaurant.json', function (jsonData) {
      vm.dataDays = jsonData.data
    })
  },
  filters: {
    // getImage (val) {
    //   const token = 'EAACEdEose0cBAAv9eGWw6Nubwyn7iwvfcIafs2cJSMyLCHOGJtyPc1Gb0cWfLHVXhZBJyi4hmcu0ZBO1ObCbZAGEb5MEwWnZAqKSBzpEIQuMQ0MwbdTiY0SAFC56B3TuHhmVTPCl2crhcnALMisJrJnSmc1ZBgi5lEvLJIj4hY208QKnTumhR'
    //   const url = ` https://graph.facebook.com/v2.9/${val}/attachments?access_token=${token}`
    //   axios.get(url)
    //   .then((response) => {
    //     const raw = response.data.data[0]
    //     try {
    //       const imagePaths = raw.subattachments.data
    //     } catch (err) {
    //       console.log('no subattachments')
    //     }
    //   })
    //   return '123'
    // }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
}

.post{
    border: 1px solid black;
    margin: 10px;
    padding: 10px;
}

.comment{
    border: 1px solid black;
    margin: 10px;
    padding: 10px;
}

.preview-img {
  width: 15%
}

.link {
  display: block
}
</style>
