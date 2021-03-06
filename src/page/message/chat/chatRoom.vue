<template>
  <div class="m-box-model p-chat-room m-pos-f">
    <header class="m-box m-pos-f m-aln-center m-main m-head-top m-bb1">
      <div class="m-flex-grow1 m-flex-base0">
        <svg class="m-style-svg m-svg-def" @click="goBack">
          <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-back"></use>
        </svg>
      </div>
      <div class="m-box m-flex-grow1 m-flex-base0 m-aln-center m-justify-center m-head-top-title">
        <span>{{ room.name || user.name }}</span>
      </div>
      <div class="m-box m-flex-grow1 m-flex-base0 m-aln-center m-justify-end">
        <svg class="m-style-svg m-svg-def" @click="goBack">
          <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#feed-more"></use>
        </svg>
      </div>
    </header>
    <main
      ref="msgs_bady"
      class="m-flex-grow0 m-box-model m-flex-shrink1 m-main"
      style=";padding: 0.9rem 0 1.3rem 0; height: 100vh; overflow-y: auto;"
    >
      <section :style="{order: msgs.length - index}" v-for="(msg, index) in msgs" :key="`msg_${msg.id}`">
        <my-msg
          :name="msg.touid !== mid ? me.name : (room.name || user.name)"
          :id="msg.touid !== mid ? ~~mid : ~~uid"
          :message="msg"
          :user="msg.touid !== mid ? me : user"
          :isMine="~~msg.touid !== ~~mid"
        />
      </section>
    </main>⁄
    <footer class="m-box-model m-justify-end m-comment-input">
      <div class="m-box m-aln-end m-comment-input-wrap">
        <span class="m-box-model m-flex-grow1 m-flex-shrink1 m-justify-end m-wz-def">
          <textarea 
            v-model='txt'
            :style="{ height: `${scrollHeight}px` }"
            ref='textarea'
            maxlength="255"
          ></textarea>
          <textarea 
            rows="1"
            v-model='shadowText'
            maxlength="255" 
            style="position: absolute; z-index: -9999; visibility: hidden;"
            ref='shadow'></textarea>
        </span>
        <div class="m-box-model m-box-justify-end" style="width: 1rem; margin: 0 0 0 15px;">
          <span class="m-wz-def" style="font-size: 10px; margin-bottom: 10px" v-if="txt.length >= 210">{{ txt.length }}/255</span>
          <button class="m-comment-submit" :disabled="!txt.length" @click='sendTxtMsg'>发送</button>
        </div>
      </div>
    </footer>
  </div>
</template>
<script>
import http from "@/http.js";
import bus from "@/bus.js";
import store from "store";
import MyMsg from "./myMsg";
import dataBase from "@/util/database.js";

const prefixCls = "chat-room";
export default {
  name: "chat-room",
  components: {
    MyMsg
  },
  data: () => ({
    txt: "",
    uid: 0,
    mid: 0,
    prefixCls,
    cid: 0,
    me: {},
    room: {},
    user: {},
    reverseMsg: [],
    scrollHeight: 0,
    curMainbodyHeight: 0
  }),
  methods: {
    // 发送文本消息
    sendTxtMsg() {
      bus.$emit("sendTxt-easemob", {
        content: this.txt,
        cid: this.cid,
        touid: this.uid,
        type: "chat",
        callback: this.sendCallback
      });
    },
    sendCallback(msg) {
      this.txt = "";
      this.$store.dispatch("PUSH_NEW_MESSAGE", msg);
    }
  },
  computed: {
    easemob() {
      return this.$store.state.easemob || {};
    },
    msgs() {
      return this.cid > 0
        ? this.easemob.MESSAGES[`room_${this.cid}`] || []
        : [];
    },
    shadowText() {
      return this.txt + "a";
    }
  },
  watch: {
    msgs() {
      this.$nextTick(() => {
        const scrollTop =
          this.$refs.msgs_bady.scrollHeight - this.$refs.msgs_bady.offsetHeight;
        this.$refs.msgs_bady.scrollTop = scrollTop;
      });
    },
    txt(val, oval) {
      if (val !== oval) {
        store.set(`H5_CHAT_SAVE_${this.cid}`, val);
        this.$nextTick(() => {
          this.$refs.shadow &&
            (this.scrollHeight = this.$refs.shadow.scrollHeight);
        });
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.$refs.shadow && (this.scrollHeight = this.$refs.shadow.scrollHeight);
    });
  },
  async beforeRouteEnter(to, from, next) {
    let uid = ~~to.params.uid;
    const me = store.get("CURRENTUSER");
    const mid = ~~me.id;
    let user = {};
    try {
      const { data } = await http.get(`users/${uid}`, {
        validateStatus: s => s === 200
      });
      user = data;
    } catch (e) {
      uid = 0;
    }

    uid > 0
      ? (async () => {
          const room = await dataBase.transaction("rw?", "room", () => {
            return dataBase.room
              .where("[mid+uid]")
              .equals([mid, uid])
              .and(item => {
                return item.del === 0;
              })
              .first();
          });
          if (room !== undefined) {
            next(vm => {
              vm.cid = room.id;
              vm.mid = mid;
              vm.uid = uid;
              vm.me = me;
              vm.room = room;
              vm.user = user;
            });
          } else {
            const time = new Date();
            const room = {
              title: mid + uid,
              type: "chat",
              mid,
              uid,
              last_message_time: time.valueOf(),
              last_message_txt: "",
              del: 0,
              name: user.name
            };
            const cid = await dataBase.transaction("rw?", "room", () => {
              return dataBase.room.add(room);
            });
            next(
              vm => (
                (vm.cid = cid),
                (vm.mid = mid),
                (vm.uid = uid),
                (vm.me = me),
                (vm.room = room),
                (vm.user = user),
                vm.$store.commit("PUSH_ROOM", { id: cid, ...room })
              )
            );
          }
        })()
      : window.history.back(-1);
  }
};
</script>
<style lang="less">
.p-chat-room {
  min-height: 100vh;
  .m-avatar-box {
    margin: 0 0.2rem;
  }
}
</style>
