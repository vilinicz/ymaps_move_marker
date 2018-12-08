<template>
  <div id="app">
    <div id="map" class="map"></div>
    <a href="https://github.com/vilinicz/ymaps_move_marker/blob/master/src/App.vue" class="link">Go to Github</a>
  </div>
</template>

<script>

export default {
  name: 'app',

  data() {
    return {
      map: null,
      bus: {
        id: '1',
        name: 'Karl Popper',
        position: [57.81108739030145, 28.324718888257294],
      },
      busMarker: null,
    }
  },

  async mounted() {
    await window.ymaps.ready();
    this.map = new window.ymaps.Map('map', {
      center: [57.81108739030145, 28.324718888257294],
      zoom: 13
    });

    const geometry = { type: 'Point', coordinates: this.bus.position};
    const properties = { hintContent: this.bus.name };
    const options = {
      iconLayout: 'default#image',
      iconImageHref: `${process.env.BASE_URL}icon_bus.svg`,
      iconImageSize: [40, 40],
      iconImageOffset: [-20, -20],
    };

    // ### CREATE MARKER AND SAVE IT TO THE VUE DATA ### //
    this.busMarker = new window.ymaps.GeoObject({ geometry, properties }, options);

    this.map.geoObjects.add(this.busMarker);
    this.updateBus();
  },

  methods: {
    updateBus() {
      setInterval(() => {

        // Imitate new coordinates from websockets
        const random1 = ((Math.floor((Math.random()*10)+1)) * (Math.random() < 0.5 ? -1 : 1) * 0.0003);
        const random2 = ((Math.floor((Math.random()*10)+1)) * (Math.random() < 0.5 ? -1 : 1) * 0.0005);
        this.bus.position[0] += random1;
        this.bus.position[1] -= random2;
        console.log('Bus updated. New position:', this.bus.position);

        // ### UPDATE MARKER COORDINATES WITHOUT RERENDER ### //
        this.busMarker.geometry.setCoordinates(this.bus.position);

      }, 2000);
    }
  },
}
</script>

<style lang="scss">
  body, html {
    margin: 0;
    padding: 0;
  }
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;

  .map {
    width: 100vw;
    height: 100vh;
  }

  .link {
    position: absolute;
    top: 3rem;
    right: 10px;
    background-color: #fff;
    padding: 0.5rem 1rem;
    box-shadow: 0 1px 6px rgba(#000, 0.25);
    z-index: 999;
    border-radius: 4px;
    font-weight: bold;
    color: dodgerblue;
    text-decoration: none;
  }
}
</style>
