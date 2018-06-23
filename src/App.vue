<template>
  <div id="app">

    <div class="list">
      <div class="list-item" v-for="(listing,index) in listings" :key="listing.id" v-if="listings && listings.length > 0 && index <= limit">
        <span class="list-item-name">{{ listing.name }}</span>
        <span class="list-item-price">{{ listing.price }} €</span>
      </div>
    </div>
    <l-map :center="center" :zoom="zoom" :attributionControl="attributionControl">
      <l-tilelayer :url="url" :attribution="attribution"></l-tilelayer>
      <l-marker v-for="listing in listings" :key="listing.id" :position="[listing.lat, listing.long]" :title="listing.name" :opacity="opacity" :draggable="false">
        <l-popup :content="formatPrice(listing.price)"></l-popup>
      </l-marker>
    </l-map>

  </div>
</template>

<script>
import rentals from './data.json'
import '../node_modules/leaflet/dist/leaflet.css'

export default {
  name: 'App',
  data () {
    return {
      listings: rentals,
      limit: 50,
      zoom: 12,
      center: [59.334591, 18.063240],
      marker: [51.500, 0.00],
      minZoom: 8,
      url: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
      attribution: 'vue-leaflet',
      title: 'vue-leaflet',
      opacity: 1,
      draggable: true,
      attributionControl: false
    }
  },
  methods: {
    formatPrice (price) {
      return price + ' €'
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Lato:300,400,700');

body {
  margin: 0;
}

#app {
  display: grid;

  grid-template-columns: 7fr 5fr;

  background: #f5f5f5;
  font-family: 'Lato', sans-serif;
}

#map {
  position: sticky !important;
  top: 0;
  bottom: 0;
  left: 0;

  height: 100vh;
}

.list {
  display: grid;

  grid-template-columns: 1fr 1fr 1fr;
  grid-gap: 20px;

  padding: 10px;
}

.list-item {
  display: block;

  padding: 20px;

  border-radius: 2px;
  background: white;
  box-shadow: 0 2px 1px rgba(170, 170, 170, 0.25);
}

.list-item-name {
  display: block;

  font-weight: 700;
}

.list-item-price {
  display: block;

  font-size: .9rem;
  font-weight: 300;

  text-align: right;

  color: rgba(170, 170, 170, 0.75);
}
</style>
