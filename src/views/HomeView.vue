
<template>
  <div class="flex">
    <div class="left">
      <!-----------Search Bar-->
      <div class="flex">
        <input type="text" v-model="searchQuery" @keyup.enter="searchLocation" placeholder="Search location">
        <button @click="searchLocation">Search</button>
      </div>
      <br />

      <!-----------Get Current Location-->
      <button @click="getCurrentLocation" :disabled="isLoading" style="display: block;">
        {{ isLoading ? 'Loading...' : 'Current Location' }}
      </button>
      <br />
      <br />

      <!-----------Time-->
      <h3>Latest Searched Location</h3>
      <TimeVue :location="lastestResult" />
    </div>

    <!-----------Map-->
    <MapVue :location=location :searchedLocations=searchedLocations />
  </div>

  <!-----------History List-->
  <br />
  <HistoryVue @deleteLocations="deleteSelectedLocations" :searchedLocations="searchedLocations" />
</template>



<script>
import MapVue from '../components/Map.vue';
import { OpenStreetMapProvider } from 'leaflet-geosearch';
import TimeVue from '../components/Time.vue';
import HistoryVue from '../components/History.vue';

export default {

  components: {
    MapVue,
    TimeVue,
    HistoryVue
  },


  data() {
    return {
      location: null,
      address: "",
      searchedLocations: [],
      searchQuery: "",
      lastestResult: null,
      isLoading: false
    };
  },//end of data


  methods: {
    //get the address based on current latitude and lognitude
    async getAddress(lat, lgt) {
      try {
        const response = await fetch(
          `https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lgt}`
        );
        const data = await response.json();
        this.address = data.display_name;
      } catch (error) {
        console.error('Error fetching address:', error);
      }
    },


    //get the location of current location from the browser
    getCurrentLocation() {
      this.isLoading = true;
      navigator.geolocation.getCurrentPosition(
        pos => {
          this.location = [pos.coords.latitude, pos.coords.longitude];
          this.getAddress(pos.coords.latitude, pos.coords.longitude);
          this.isLoading = false;
        },
        err => {
          //this.errorStr = err.message;
          this.isLoading = false;
        }
      );
    },


    //search the location based on user's input
    async searchLocation() {

      if (this.searchQuery == "") return alert("Please input something!!");//alert user if input is empty

      const provider = new OpenStreetMapProvider();
      const results = await provider.search({ query: this.searchQuery });

      if (results.length == 0) {
        alert("Your input address is invalid"); //alert user if there has no result found
      }
      else {
        this.location = [results[0].y, results[0].x];
        this.address = results[0].label;
      }

    },

    // Remove selected locations from searchedLocations array
    deleteSelectedLocations(selectedLocations) {
      this.searchedLocations = this.searchedLocations.filter(location => {
        return !selectedLocations.includes(location.name);
      });
      this.address = "";
    },

  },//end of method


  watch: {
    //execute the function when address value changed
    address: function () {
      if (this.address == "") return; //do nothing if address not existed

      const newLocation = { name: this.address, latitude: this.location[0], longitude: this.location[1] };
      this.lastestResult = newLocation;//save new location as latest searched result

      const index = this.searchedLocations.map(function (e) {//get the repeated element's index value
        return e.name;
      }).indexOf(newLocation.name);

      if (index != -1) this.searchedLocations.splice(index, 1);//remove duplication

      this.searchedLocations.unshift(newLocation);//add to list
    }
  }//end of watch


};
</script>






