<template>
  <div
    class="coin w-10 h-10 md:w-12 md:h-12 "
    @click="handleSelectCoin"
    :class="{ 'coin-selected': this.value == this.$store.state.coin }"
  >
    <svg
      version="1.0"
      xmlns="http://www.w3.org/2000/svg"
      width="100%"
      height="100%"
      viewBox="30 0 200.000000 200.000000"
      preserveAspectRatio="xMidYMid meet"
    >
      <g
        transform="translate(0.000000,200.000000) scale(0.100000,-0.100000)"
        fill="#fff"
        stroke="none"
      >
        <circle cx="1261" cy="1006" r="945"></circle>
      </g>
      <g
        transform="translate(0.000000,200.000000) scale(0.100000,-0.100000)"
        v-bind:fill="fillColor"
        stroke="none"
      >
        <path
          d="M1169 1950 c-101 -10 -179 -29 -179 -44 0 -7 20 -54 45 -106 25 -51
44 -98 41 -105 -2 -6 -23 -17 -47 -24 -24 -7 -79 -33 -122 -58 -43 -24 -83
-43 -89 -41 -5 2 -31 46 -56 98 -26 52 -53 95 -61 95 -18 0 -116 -87 -172
-153 -64 -75 -135 -204 -166 -299 -15 -45 -25 -84 -22 -87 2 -3 51 -9 107 -14
57 -5 108 -14 113 -19 6 -6 5 -29 -1 -62 -5 -30 -10 -91 -10 -137 0 -45 -4
-86 -10 -89 -5 -3 -45 -1 -87 4 -43 6 -92 7 -109 3 l-31 -7 23 -103 c24 -106
86 -253 142 -334 42 -62 122 -149 132 -143 4 2 33 43 65 90 32 47 64 85 71 85
7 0 33 -17 57 -39 25 -21 75 -55 111 -75 36 -20 66 -43 66 -50 0 -7 -27 -51
-60 -99 -33 -47 -60 -88 -60 -90 0 -8 101 -46 165 -62 91 -24 238 -35 324 -25
83 10 181 29 181 36 0 2 -23 50 -51 105 -28 56 -47 105 -42 109 4 4 40 20 78
35 39 16 94 43 124 62 42 27 56 32 62 21 5 -7 30 -53 55 -103 26 -49 48 -92
50 -94 7 -8 111 85 169 150 33 37 81 104 106 148 44 76 99 212 99 244 0 12
-24 16 -116 22 l-116 7 8 67 c5 36 9 102 9 146 l0 80 123 -3 c114 -2 123 -1
118 15 -3 10 -8 41 -12 68 -15 110 -90 287 -164 388 -37 51 -109 127 -119 127
-5 0 -35 -39 -67 -86 -32 -48 -64 -88 -70 -91 -7 -2 -38 16 -71 40 -32 25 -83
58 -113 75 -30 16 -56 34 -57 38 -2 5 26 52 61 104 l64 95 -33 17 c-51 27
-170 56 -270 68 -49 5 -94 9 -100 9 -5 -1 -44 -5 -86 -9z m214 -315 c137 -29
278 -112 363 -214 98 -117 164 -289 164 -426 0 -79 -30 -197 -72 -284 -118
-246 -409 -390 -684 -340 -261 47 -467 246 -524 506 -24 107 -25 161 -4 261
49 240 248 446 474 491 30 6 64 13 75 15 46 9 144 5 208 -9z"
        />
        <path
          d="M1111 1614 c-179 -39 -331 -161 -416 -332 -86 -175 -85 -365 5 -548
77 -157 208 -268 390 -330 43 -15 83 -19 180 -19 109 1 134 4 198 27 300 108
466 391 409 698 -66 351 -415 581 -766 504z m261 -10 c5 -3 8 -12 8 -19 0 -12
-15 -13 -82 -8 -46 3 -85 7 -87 9 -2 2 -1 11 3 20 5 14 16 16 78 9 40 -3 76
-9 80 -11z m-295 -39 c3 -12 -137 -86 -149 -79 -22 14 -2 38 55 65 62 29 87
33 94 14z m327 -21 c192 -49 354 -218 401 -415 65 -277 -108 -572 -383 -653
-182 -53 -392 -10 -518 106 -60 56 -137 172 -163 249 -28 81 -38 208 -22 291
38 194 196 362 396 419 77 22 209 24 289 3z m197 -34 c53 -37 76 -70 49 -70
-21 0 -137 85 -133 98 7 19 20 15 84 -28z m-771 -118 c0 -15 -87 -131 -98
-132 -22 0 -25 29 -7 58 45 71 65 90 85 85 11 -3 20 -8 20 -11z m978 -117 c36
-74 38 -85 17 -85 -16 0 -88 133 -78 144 16 16 31 1 61 -59z m-1116 -225 c-4
-79 -11 -96 -36 -87 -12 5 -14 19 -10 74 7 87 10 95 31 91 16 -3 18 -13 15
-78z m1176 -8 c9 -6 10 -28 6 -85 -6 -66 -10 -77 -25 -77 -16 0 -19 8 -19 48
0 57 10 122 19 122 3 0 12 -4 19 -8z m-1120 -289 c30 -68 32 -83 9 -83 -12 0
-77 119 -77 140 0 6 8 10 19 10 13 0 27 -19 49 -67z m1057 -23 c3 -6 -11 -37
-31 -70 -29 -47 -42 -60 -58 -58 -29 4 -25 26 18 86 37 50 58 63 71 42z m-864
-197 c56 -34 72 -60 43 -70 -16 -5 -136 86 -129 97 10 16 24 12 86 -27z m654
-43 c-11 -17 -134 -72 -145 -65 -24 15 -5 37 58 69 56 29 69 32 81 21 7 -7 10
-19 6 -25z m-321 -60 c14 -5 26 -17 26 -26 0 -15 -9 -16 -77 -10 -78 6 -101
15 -89 35 9 14 106 14 140 1z"
        />
      </g>
      <g v-bind:fill="fillColor" stroke="none">
        <text letter-spacing="-5" font-size="70" x="125" y="122" text-anchor="middle">
          {{ value >= 1000 ? `${value / 1000}K` : value }}
        </text>
      </g>
    </svg>
  </div>
</template>
<script>
export default {
  props: {
    fillColor: {
      type: String,
      default: "yellow",
    },
    value: {
      type: Number,
      default: 0.5,
    },
    maxValue: {
      type: Number,
      default: 1000,
    },
  },
  methods: {
    handleSelectCoin() {
      this.$store.commit("setSelectedCoin", this.value);
    },
  },
  mounted() {},
};
</script>
<style scoped>
.coin {
  transition: transform;
  transition-duration: 0.5s;
}

.coin:hover,
.coin-selected {
  animation-duration: 3s;
  animation-name: scaleAnimation;
  animation-iteration-count: infinite;
}

@keyframes scaleAnimation {
  0% {
    transform: scale(1.2, 1.2);
  }

  50% {
    transform: scale(1.3, 1.3);
  }

  100% {
    transform: scale(1.2, 1.2);
  }
}
</style>
