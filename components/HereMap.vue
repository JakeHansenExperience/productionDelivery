
<template>
  <div id="map">
  <!--In the following div the HERE Map will render-->
    <div id="mapContainer" style="height:300px;width:100%" ref="hereMap"></div>
  
  </div>
</template>




<script>
export default {
  name: "HereMap",
  props: {
    center: Object,
    points: Array,
    lat: Number,
    long: Number,
    // center object { lat: 40.730610, lng: -73.935242 }
  },
  data() {
    return {
      platform: null,
      apikey: "fW5RUjAiwVlYRH-7jcrMfPrXYxLZY-SlEdqQzaPlzYQ"
      // You can get the API KEY from developer.here.com
    };
  },
  async mounted() {
    // Initialize the platform object:
    const platform = new window.H.service.Platform({
      apikey: this.apikey
    });
    this.platform = platform;
    this.initializeHereMap();
  },
  
//   https://geocoder.ls.hereapi.com/6.2/geocode.json
// ?apiKey={YOUR_API_KEY}
// &searchtext=200%20S%20Mathilda%20Sunnyvale%20CA
  methods: {
    // async grabCoordinates() {
    //   var houseNumber = '425';
    //   var street= "randolph";
    //   var city = "chicago";
    //   var country = "usa";
    //   const response = await this.$axios.$get('https://');
    //   this.karens = response
    //   console.log("Karens")
    //   console.log(this.karens)
    // },
    initializeHereMap() { // rendering map

      const mapContainer = this.$refs.hereMap;
      const H = window.H;
      // Obtain the default map types from the platform object
      var maptypes = this.platform.createDefaultLayers();

      // Instantiate (and display) a map object:
      var map = new H.Map(mapContainer, maptypes.vector.normal.map, {
        zoom: 10,
        center: this.center
        // center object { lat: 40.730610, lng: -73.935242 }
      });

      addEventListener("resize", () => map.getViewPort().resize());

      // add behavior control
      new H.mapevents.Behavior(new H.mapevents.MapEvents(map));

      // add UI
      H.ui.UI.createDefault(map, maptypes);
      //geocode address
    

      //add post oak marker to map
      var icon = new H.map.Icon('v.png')
      var postOakMarker = new H.map.Marker({lat:29.7538 , lng:-95.4627 }, {icon: icon});
      map.addObject(postOakMarker)

     // add the point to the map
      console.log("lat: ", this.lat)
      var houstonMarker = new H.map.Marker({lat:this.lat, lng:this.long})
      map.addObject(houstonMarker)
      // console.log("Hereasas")
      // for (let i = 0; i < this.points.length; i++){
      //   console.log("Here")
      //   console.log(this.points[i])
      //   console.log(this.points[i]['lat'])
      //   var marker = new H.map.Marker({lat:this.points[i]['lat'], lng:this.points[i]['long']})
      //   map.addObject(marker)
      // }
      // End rendering the initial map
    }
  }
};
</script>

<style scoped>
#map {
  width: 60vw;
  min-width: 360px;
  text-align: center;
  margin: 5% auto;
  background-color: #ccc;
}
</style>