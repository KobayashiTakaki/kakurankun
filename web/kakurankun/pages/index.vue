<template>
  <div>
    <div class="row">
      <div class="col">
        <h2 class="my-3">生活圏かくらん君</h2>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <h3>電車遅延情報</h3>
      </div>
    </div>
    <div class="row">
      <div class="col">
        <p v-if="delayInfos.length > 0">
          以下の路線が遅延しているようです。あたかもその路線を使っているようなツイートをしてみよう。
        </p>
        <p v-else>
          遅延の情報がないようです。
        </p>
      </div>
    </div>
    <div v-if="delayInfos.length > 0" class="row">
      <div class="col">
        <ul class="list-group list-group-flush w-100">
          <li v-for="delayInfo in delayInfos"
              v-bind:key="delayInfo.id"
              class="list-group-item">
            {{ delayInfo.route_name }}
            <span class="float-right">
              <a :href="tweetLink(delayInfo.route_name)"
                  v-on:click="pickText()"
                  class="btn btn-light"
                  target="_blank">
                ツイートする
              </a>
            </span>
          </li>
        </ul>
      </div>
    </div>
    <div class="row mt-3">
      <div class="col">
        <hr>
        <p>
          更新日時: {{ displayTime(updated_at) }}
        </p>
        <p>
          情報元:
          <a href="https://rti-giken.jp/fhc/api/train_tetsudo/" target="_blank" rel="noopener nofollow">
            鉄道遅延情報のjson
          </a>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  import moment from 'moment'
  export default {
    asyncData () {
      return axios.get('https://script.google.com/macros/s/AKfycbzJcR_f3cg2QegUWwVjc7yg_gsudm0ciauUgKUW_TRNmame3_A/exec')
      .then((res) => {
        return {
          updated_at: res.data.updated_at,
          delayInfos: res.data.delayInfos
        }
      })
    },
    data () {
      return {
        texts: [
          'めっちゃ遅れてるやん',
          '遅れてる？',
          '遅れてる！',
          '遅延かー'
        ],
        text: ''
      }
    },
    methods: {
      pickText: function () {
        this.text = this.texts[Math.floor(Math.random() * this.texts.length)]
      },
      tweetLink: function (routeName) {
        const url = 'https://twitter.com/intent/tweet'
        const text = routeName + this.text
        return `${url}?text=${text}`
      },
      displayTime: function (unixtime) {
        return moment(unixtime).utc().add(9,'hours').format("YYYY-MM-DD HH:mm")
      }
    },
    mounted: function () {
      setTimeout(function () {
        location.reload()
      }, 30000)
    }
  }
</script>

<style>
  html, body {
    height: 100%;
    font-family: 'Kosugi Maru', sans-serif;
    color: #333333;
  }
  body:after {
    content: "";
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(#FAFAFA 18%, #A0E9DC 79%) no-repeat;
    z-index: -1;
  }
  li.list-group-item {
    background-color: rgb(0, 0, 0, 0)
  }
</style>