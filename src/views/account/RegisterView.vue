<template>
  <div class="ui main container">
    <div class="ui centered grid">
      <div class="column">
        <h1 class="ui center aligned header">注册</h1>
        <form class="ui form error" novalidate @submit.prevent="submit()">
          <div class="field">
            <input
              type="text"
              name="username"
              placeholder="请输入用户名"
              v-model="params.username">
          </div>
          <div class="field">
            <input
              type="email"
              name="email"
              placeholder="请输入邮箱"
              v-model="params.email">
          </div>
          <div class="field">
            <input
              type="password"
              name="password"
              placeholder="请输入密码"
              v-model="params.password">
          </div>
          <div class="field">
            <div class="ui checkbox">
              <input type="checkbox">
              <label>
                我已阅读并同意
                <router-link target="_blank" :to="{ name: 'article_detail', params: { 'slug': 'tos' } }">服务条款</router-link> 与
                <router-link target="_blank" :to="{ name: 'article_detail', params: { 'slug': 'privacy' } }">隐私政策</router-link>。
              </label>
            </div>
          </div>
          <div class="ui error message" v-if="failure">
            <div class="header">{{failure.message}}</div>
            <ul class="list">
              <li v-for="error in failure.errors">{{error.message}}</li>
            </ul>
          </div>
          <div class="field">
            <button class="fluid ui primary button" type="submit">注册</button>
          </div>
          <div class="ui grid">
            <div class="center aligned sixteen wide column">
              已有账号？<router-link to="/account/login">点此登录</router-link>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex';

const NProgress = global.NProgress;

export default {
  data() {
    return {
      rules: {
      },
      params: {
        username: '',
        email: '',
        password: '',
      },
    };
  },
  computed: mapState({
    success: state => state.account.register.success,
    failure: state => state.account.register.failure,
  }),
  methods: {
    submit() {
      NProgress.start();
      this.$store.dispatch('accountRegisterSubmit', this.params);
    },
    successWatcher(val, oldVal) {
      if (val && !oldVal) {
        NProgress.done();
        const redirectUrl = this.$route.query.redirect_url || '/';
        this.$router.push(redirectUrl);
      }
    },
    failureWatcher() {
      NProgress.done();
    },
  },
  watch: {
    success: 'successWatcher',
    failure: 'failureWatcher',
  },
  beforeCreate() {
    this.$store.dispatch('accountRegisterInit');
  },
};
</script>

<style lang="scss" scoped>
.column {
  max-width: 400px;
}
</style>
