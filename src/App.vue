<template>
  <div id="app">

    <div class="list">
      <div class="list-item" v-for="(listing, key) in listings" :key="key" @click="() => {focus(listing);toggle(listing,key)}" v-bind:class="{focus: current === listing}">
        <span class="list-item-name">{{ listing.street }}</span>
        <span class="list-item-price">{{ formatPrice(listing.price) }}</span>
      </div>
    </div>
    <GmapMap ref="map" :center="{lat:59.334591, lng:18.063240}" :zoom="12" map-type-id="terrain" class="map">
      <GmapInfoWindow :options="info.options" :position="info.position" :opened="info.opened" @closeclick="info.opened = true">
        <div class="map-info-street">{{ info.street }}</div>
        <div class="map-info-price">{{ info.price }}</div>
      </GmapInfoWindow>
      <GmapMarker ref="marker" :key="key" v-for="(marker, key) in listings" :position="marker.position" :icon="{url:'https://raw.githubusercontent.com/orestisrodriguez/vuebnb/master/src/assets/marker.png', scaledSize: new google.maps.Size(25,34.5)}" :clickable="true" @click="() => {focus(marker);toggle(marker,key)}"></GmapMarker>
    </GmapMap>

  </div>
</template>

<script>
import rentals from './data.json'
import {gmapApi} from 'vue2-google-maps'

export default {
  name: 'App',
  data () {
    return {
      listings: rentals,
      current: null,
      limit: 50,
      info: {
        options: {
          pixelOffset: {
            width: 0,
            height: -35
          }
        },
        position: null,
        street: null,
        price: null,
        opened: false,
        currentKey: null
      }
    }
  },
  computed: {
    google: gmapApi
  },
  methods: {
    formatPrice (price) {
      return price + ' €'
    },
    focus (item) {
      // make item active
      this.current = item

      // move map to marker
      this.$refs.marker.forEach((e) => {
        if (item.position.lat === e.$options.propsData.position.lat && item.position.lng === e.$options.propsData.position.lng) {
          this.$refs.map.panTo(item.position)
        }
      })
    },
    toggle (marker, key) {
      this.info.position = marker.position
      this.info.street = marker.street
      this.info.price = this.formatPrice(marker.price)
      if (this.info.currentKey === key) {
        this.info.opened = !this.info.opened
      } else {
        this.info.opened = true
        this.info.currentKey = key
      }
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

.map {
  position: sticky !important;
  top: 0;
  bottom: 0;
  left: 0;

  height: 100vh;
}

.map-info-street {
  font-family: 'Lato', sans-serif;

  font-weight: 700;

  margin-bottom: 5px;
}

.map-info-price {
  font-family: 'Lato', sans-serif;

  font-weight: 300;
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

  cursor: pointer;
}

.list-item.focus {
  box-shadow: 0 4px 3px rgba(170, 170, 170, 0.5);
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
