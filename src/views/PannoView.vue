<template>

  <Panno></Panno>

</template>

<script>

import request from "@/utils/request";
import Panno from "@/components/Panno.vue";
import { mapState } from 'vuex';
export default {
  name: "PannoView",
  components: { Panno },
  data() {
    return {
      ws: null,
      isConnected: false,
      selected: [],
    }
  },
  computed: mapState(['roundStatus', 'updated']),

  watch: {
    updated(status, old) {
      // console.log(status, old)

      if (status.length === 0 && this.$store.state.betAction === 'add') {
        this.clearAll();
      }
      if (status.length > 0) {
        this.uploadBets();
      }
    },
    roundStatus(status, old) {
      console.log(status, old)
      if (status === "started") {

        this.$store.commit('setGameStatus', 'BET');

        this.getUserBalance();
        this.loadGetTodayBets();
        // this.getLastBet();
      }

      // if(status === 'wait'){
      //    this.uploadBets();
      // }

    }
  },
  mounted() {
    this.initWebsocket();
    this.loadLastWinNumbers();
    this.getHotCoolNumbers();
    this.getUserBalance();
    this.loadCurrentGame();
    setInterval(() => {
      if (this.isConnected) {
        const ping = { "type": "ping" };
        console.log("ping...")
        this.ws.send(JSON.stringify(ping));
      }

    }, 30000);
  },
  methods: {
    async loadCurrentGame() {
      try {
        const response = await request.post('api/game/getLastOrder');
        const vm = this;
       
        if (response.data.result && response.data.message === 'success' && response.data.result.type === 'game') {
          const { seqPlay, status, number, dealer} = response.data.result;

          vm.$store.commit('setSeqPlay', seqPlay);
          const roundInfo = {
            dealer,
            seqPlay
          };
          console.log(response.data.result)
          vm.$store.commit('setRoundInfo', roundInfo);
          if (status === 'go') {
            // vm.$store.commit("setRoundStatus", "started");
          }
          if (status === 'stop') {
            // no betting
            vm.$store.commit("setRoundStatus", "wait");
          }
          if (status === 'end') {
            // end
            vm.$store.commit("setRoundStatus", "end");
          }

          if (status === 'result') {
            let num = number;
            if (num.slice(0, 1) == 0) {
              num = num.slice(1, 2)
            }
            vm.selected = vm.$store.state.selected.slice(0, vm.$store.state.selected.length);
            vm.$store.commit("setSelected", []);
            vm.$store.commit("setWinNumber", parseInt(num));
            vm.$store.commit("setRoundStatus", "result");
            // vm.updataNum(num)
          }
        }

      }
      catch (err) {
        console.log(err)
      }
    },
    async loadGetTodayBets() {
      try {
        const response = await request.post('api/member/getTodayBet', {}, { headers: this.getAxoisTokenHeader() });
        const data = [];
        console.log(response);
        if (response.data.message === 'success' && response.data.result) {

          for (const row of response.data.result) {
            data.push({ game: row.seqplay, date: row.bet_time, bet: row.bet_amount, win: row.win_amount, result: '' });
          }

        }
        // const response = await request.post('/member/getAmount', {}, { headers: this.getAxoisTokenHeader() });
        console.log(data);
        this.$store.commit("setHistoryData", data);
        // console.log(response)
      }
      catch (err) {
        console.log(err)
      }
    },
    async loadLastWinNumbers() {
      try {
        const response = await request.post('api/game/openCode');
        const _numbers = [];
        if (response.data.result && Array.isArray(response.data.result)) {
          _numbers.push(response.data.result.map((round, index) => (`${round.open_code}`)))
        }

        this.$store.commit("setLoadedWinNumbers", _numbers[0].reverse());
      }
      catch (err) {
        console.log(err)
      }
    },
    clearAll() {
      if (this.isConnected) {
        console.log('send cancle all')
        this.ws.send(JSON.stringify({ type: 'all_cancel', seqPlay: `${this.$store.state.seqPlay}` }));
      }

    },
    uploadBets() {

      // this.$store.commit('setGameStatus','WIN');
      // this.$store.commit('setRoundBalance',eval('1230'));
      if (this.isConnected) {
        for (const selected of this.$store.state.updated) {
          if (selected.refer.startsWith('o')) {
            continue;
          }
          const bet = {
            type: (this.$store.state.betAction === 'remove' ? 'cancel' : 'bet'),
            seqPlay: `${this.$store.state.seqPlay}`,
            bet_code: selected.refer,

          }
          if (this.$store.state.betAction === 'add') {
            bet.bet_amount = selected.value;
          }
          console.log(bet)
          this.ws.send(JSON.stringify(bet));
        }
      }
    },
    initWebsocket() {
      const vm = this;
      if (this.ws == null) {
        this.ws = new WebSocket("ws://localhost:5600/wss?token=" + this.getUserToken());
        this.ws.onopen = function () {
          // vm.isLogin = true;
          vm.isConnected = true;

        };
        this.ws.onmessage = function (evt) {
          // oUl.innerHTML += "<li>" + evt.data + "</li>";

          let data = JSON.parse(evt.data)
          console.log(data, " is received from ws")
          if (data.type === 'win' && data.amount > 0) {
            vm.$store.commit('setGameStatus', 'WIN');
            const selected = vm.selected.filter(f => {
              const ele = document.getElementById(f.refer);
              if (ele && ele != null) {
                const hover = ele.getAttribute('hover');
                const arr = hover.includes(" ") ? hover.split(" ") : [hover];
                return arr.includes(`PL${vm.$store.state.winNumber}`);
              }
              return false;

            });
            if (selected.length > 0) {
              for (const s of selected) {
                s.value = data.amount;
              }
            }
            vm.$store.commit('setSelected', selected);
            vm.$store.commit('setWinCoin', { refer: data.bet_code, value: data.amount });
            vm.$store.commit('setRoundBalance', eval(data.amount));
          }
          // get game number;

          if (data.type === 'game') {
            if (data.status === 'go') {
              // start round
              vm.$store.commit("setRoundStatus", "started");

              vm.$store.commit('setSeqPlay', data.seqPlay);
              const roundInfo = {
                dealer: data.dealer,
                seqPlay: data.seqPlay
              };
              vm.$store.commit('setRoundInfo', roundInfo);

            }
            if (data.status === 'stop') {
              // no betting
              vm.$store.commit("setRoundStatus", "wait");
            }
            if (data.status === 'end') {
              // end
              vm.$store.commit("setRoundStatus", "end");
            }

            if (data.status === 'result') {
              let num = data.number
              if (num.slice(0, 1) == 0) {
                num = num.slice(1, 2)
              }
              vm.selected = vm.$store.state.selected.slice(0, vm.$store.state.selected.length);
              vm.$store.commit("setSelected", []);
              vm.$store.commit("setWinNumber", parseInt(num));
              vm.$store.commit("setRoundStatus", "result");
              // vm.updataNum(num)
            }
          }
        };
        this.ws.onclose = function () {
          // oUl.innerHTML += "<li>客户端已断开连接</li>";
          vm.isConnected = false;
          console.log('web socket is closed', this.ws)
        };
        this.ws.onerror = function (evt) {
          vm.isConnected = false;
          // oUl.innerHTML += "<li>" + evt.data + "</li>";
          console.log(evt.data, " is ws error")
        };

      }

    },
    getAxoisTokenHeader() {
      const headers = {
        'Content-Type': 'application/json',
        'token': this.getUserToken()
      }
      return headers;
    },
    getUserToken() {
      let token = this.$store.state.token;
      if (token == '')
        token = localStorage.getItem('userToken');
      // console.log(token)
      return token;
    },

    async getUserBalance() {
      try {
        const response = await request.post('api/member/getAmount', {}, { headers: this.getAxoisTokenHeader() });
        // const response = await request.post('/member/getAmount', {}, { headers: this.getAxoisTokenHeader() });
        this.$store.commit("setBalance", response.data.result.amount);
        // this.$store.commit("setBalance", 1000);
        // console.log(response)
      }
      catch (err) {
        console.log(err)
      }
    },
    async getHotCoolNumbers() {
      try {
        const response = await request.post('api/game/codeRank');
        // const response = await request.post('/game/codeRank');
        this.$store.commit("setHotCoolNumbers", response.data.result);
        // console.log(response)
      }
      catch (err) {
        console.log(err)
      }
    }
  }

};
</script>

<style scoped>
</style>
