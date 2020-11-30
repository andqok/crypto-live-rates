<template>
  <div
      class="crypto-item"
      @click="$emit('set-base', name['CoinInfo']['Internal'])"
      v-bind:class="{ active: isBase }">
    <img
        class="image"
        alt=""
        v-bind:src="'https://www.cryptocompare.com/' + name['CoinInfo']['ImageUrl']">
    <span class="name">{{name['CoinInfo']['FullName']}}</span>
    <span class="ticker">{{name['CoinInfo']['Internal']}}</span>
    <span class="rate">{{data}}</span>
  </div>
</template>

<script>

export default {
  name: 'CryptoItem',
  props: {
    name: Object,
    isBase: Boolean,
    data: Number,
  },
}

</script>

<style scoped>
.image {
  width: 2em;
  grid-area: icon;
  margin: auto;
  position: relative;
}

.name {
  grid-area: name;
}
.ticker, .name {
  margin-left: .5rem;
}
.ticker {
  grid-area: ticker;
  font-weight: 900;
}
.rate {
  grid-area: rate;
  line-height: 2rem;
}
.crypto-item {
  height: 5rem;
  padding: .5rem;
  display: grid;
  background: #fff;
  transition: transform .1s ease-out;
  border-radius: 4px / 8px;
  box-shadow: inset 1px 1px 1px;
  grid-template-columns: 3.5rem 1fr .8fr;
  grid-template-areas: "icon ticker rate"
                       "icon name   sparkline";
}
.crypto-item.active {
  box-shadow: inset 1px 1px 1px, 0 0 0 2px red;;
  transform: scale(1.03);
}

.crypto-item:not(.active):hover {
  transform: scale(1.03);
  cursor: pointer;
}

span {
  text-align: left;
  line-height: 2rem;
}

</style>
