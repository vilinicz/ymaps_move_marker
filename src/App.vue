<template>
  <div id="app">
    <div id="map" class="map"></div>
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
        status: 'On the road',
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

    const geometry = { type: 'Point', coordinates: [this.bus.position[0], this.bus.position[1]] };
    const properties = { hintContent: `${this.bus.name}` };
    const options = {
      iconLayout: 'default#image',
      iconImageHref: `${process.env.BASE_URL}icon_bus.svg`,
      iconImageSize: [40, 40], // [ширина, высота]
      iconImageOffset: [-20, -20],
    };
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
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;

  .map {
    width: 100vw;
    height: 100vh;
  }
}
</style>
