<template>
  <div class="p-profile">
    <header class="m-box m-aln-center m-justify-center m-main m-pos-f m-bb1 m-head-top">我</header>
    <main class="m-box-model" style="padding-top: .9rem">
      <div class="m-box-model m-main m-pr-info">
        <router-link tag="section" class="m-box m-aln-center" to="/info">
          <avatar :user="user" size="big" />
          <div class="m-text-box m-flex-grow1 m-flex-shrink1 m-flex-base0 m-pr-user-info">
            <h4 class="m-pr-username">{{ user.name }}</h4>
            <p class="m-pr-bio">{{ user.bio }}</p>
          </div>
          <svg class="m-style-svg m-svg-def m-entry-append">
            <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
          </svg>
        </router-link>
        <div class="m-box m-aln-center m-justify-aro m-bt1 m-pr-extra-box">
          <router-link
          tag="div"
          :to="`/user/${user.id}/fans`"
          class="m-box-model m-aln-center m-justify-center m-flex-grow1 m-pr-extra">
            <v-badge count='0'>
              <a>{{ ~~(extra.followers_count) | formatNum }}</a>
            </v-badge>
            <p>粉丝</p>
          </router-link>
          <router-link
          tag="div"
          :to="`/user/${user.id}/fans`"
          class="m-box-model m-aln-center m-justify-center m-flex-grow1 m-pr-extra">
            <v-badge count='0'>
              <a>{{ ~~(extra.followings_count) | formatNum }}</a>
            </v-badge>
            <p>关注</p>
          </router-link>
        </div>
      </div>
      <div class="m-box-model m-pr-entrys">
        <ul class="m-box-model m-entry-group">
          <router-link :to="`/user/${user.id}`" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-home"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">个人主页</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
          <router-link to="/upgrade" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-collect"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">收藏</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
          <router-link to="/upgrade" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-plane"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">我的投稿</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
          <router-link to="/upgrade" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-question"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">我的问答</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
          <router-link to="/own/groups" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-group"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">我的圈子</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
        </ul>
        <ul class="m-box-model m-entry-group">
          <router-link to="/wallet" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-wallet"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">钱包</span>
            <span class="m-entry-extra">{{ balance }}</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
          <router-link to="/upgrade" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-integral"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">积分</span>
            <span class="m-entry-extra">{{ new_balance }}</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
        </ul>
        <ul class="m-box-model m-entry-group">
          <router-link to="/upgrade" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-approve"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">认证</span>
            <span class="m-entry-extra">{{ verified ? '已认证' : '未认证' }}</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
          <router-link to="/setting" tag="li" class="m-entry">
            <svg class='m-style-svg m-svg-def m-entry-prepend'>
              <use xlink:href="#profile-setting"></use>
            </svg>
            <span class="m-text-box m-flex-grow1">设置</span>
            <svg class="m-style-svg m-svg-def m-entry-append">
              <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-arrow-r"></use>
            </svg>
          </router-link>
        </ul>
      </div>
    </main>
    <foot-guide/>
  </div>
</template>
<script>
export default {
  name: "profile",
  data() {
    return {};
  },
  computed: {
    user() {
      return this.$store.state.CURRENTUSER || {};
    },
    extra() {
      return this.user.extra || {};
    },
    wallet() {
      return this.user.wallet || {};
    },
    new_wallet() {
      return this.user.new_wallet || {};
    },
    new_balance() {
      return (~~this.new_wallet.balance).toFixed(2);
    },
    balance() {
      return (~~this.wallet.balance).toFixed(2);
    },
    verified() {
      return this.user.verified;
    }
  },
  methods: {
    RefreshUserData() {
      const id = this.user.id;
      this.$http.get(`users/${id}`).then(({ data = {} }) => {
        data &&
          data.id === id &&
          this.$store.commit(
            "SAVE_CURRENTUSER",
            Object.assign({}, this.user, data)
          );
      });
    }
  },
  mounted() {
    this.RefreshUserData();
  }
};
</script>
<style lang="less">
.m-pr-info {
  padding: 30px;
}
.m-pr-extra {
  font-size: 28px;
  p {
    margin-top: 15px;
  }
  & + & {
    border-left: 1px solid @border-color;
  }
  &-box {
    margin-top: 30px;
    padding-top: 30px;
  }
}
.m-pr-user-info {
  margin-left: 30px;
  margin-right: 30px;
  line-height: 40px;
  .m-pr-username {
    font-size: 32px;
    color: @text-color1;
  }
  .m-pr-bio {
    overflow: hidden;
    max-height: 40 * 2px;
    font-size: 28px;
    color: @text-color3;
    text-overflow: ellipsis;
  }
}

.m-pr-entrys {
  margin-top: 20px;
  margin-bottom: 20px;
  .m-entry-group {
    padding: 0 20px;
  }
}
</style>
