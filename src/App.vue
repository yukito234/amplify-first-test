<template>
  <div id="app">
      <div v-if="signedIn">
        <div id="nav">
          <div class="amplify-sign-out">
            <amplify-sign-out/>
            ようこそ、{{username}} さん
          </div>
          <li><router-link to="/ai">AIアプリ</router-link></li>
          <li><router-link to="/chat">チャットアプリ</router-link></li>
          
        </div>
        <router-view />
    </div>
      <div v-else>
          <amplify-authenticator />
        </div>
  </div>
  <!--
    <div id="app">
      <amplify-authenticator />
      <div id="nav">
        <li><router-link to="/ai">AIアプリ</router-link></li>
        <li><router-link to="/chat">チャットアプリ</router-link></li>
        <router-view />
      </div>
    </div>
  -->
</template>

<script>
import { Auth } from 'aws-amplify'
import { AmplifyEventBus } from 'aws-amplify-vue'

window.LOG_LEVEL = 'VERBOSE';

export default {
  name: 'app',
  data(){
    return {
      signedIn: false,
      username: ''
    }
  },
  async beforeCreate() {
    // Auth.currentAuthenticatedUser()でユーザ情報を取得する。
    // 取得できなければ認証ステータスをfalseに設定する
    try {
      let cognitoUser = await Auth.currentAuthenticatedUser()
      this.signedIn = true
      this.username = cognitoUser.username
    } catch (err) {
      this.signedIn = false
    }
    // 認証ステータスが変わった時に呼び出されるイベントを登録
    AmplifyEventBus.$on('authState', async  info => {
      if (info === 'signedIn') {
        let cognitoUser = await Auth.currentAuthenticatedUser()
        this.signedIn = true
        this.username = cognitoUser.username
      } else {
        this.signedIn = false
      }
    });
  }
};
</script>

<style src="./assets/style.css" />
