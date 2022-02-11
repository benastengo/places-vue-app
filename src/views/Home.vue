<script>
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      newPlacesParams: {},
      currentPlace: {},
      updatePlace: {},
    };
  },
  created: function () {
    this.placesIndex();
  },
  methods: {
    placesIndex: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log("Sanity Check", response.data);
        this.places = response.data;
      });
    },
    placesCreate: function () {
      axios
        .post("http://localhost:3000/places", this.newPlacesParams)
        .then((response) => {
          console.log("Places Create", response);
          this.places.push(response.data);
          this.newPlacesParams = {};
        })
        .catch((error) => {
          console.log("Places create error", error.response);
        });
    },
    placesShow: function (place) {
      this.currentPlace = place;
      this.updatePlace = place;
      document.querySelector("#place-details").showModal();
    },
    placesUpdate: function (place) {
      axios
        .patch(`http://localhost:3000/places/${place.id}`, this.updatePlace)
        .then((response) => {
          console.log("Places Update", response);
          this.currentPlace = {};
        })
        .catch((error) => {
          console.log("places update error", error.response);
        });
    },
    placesDestroy: function (place) {
      if (confirm("Are you sure? This cannot be undone")) {
        axios.delete(`http://localhost:3000/places/${place.id}`).then((response) => {
          console.log("Place data destroyed", response);
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        });
      }
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>Add some places if you wanna</h1>
    <div>
      Name of Place:
      <input type="text" v-model="newPlacesParams.name" />
      Address:
      <input type="text" v-model="newPlacesParams.address" />
      <button v-on:click="placesCreate()">Create Place</button>
    </div>
    <h1>{{ "Here are some of Bens favorite places" }}</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <button v-on:click="placesShow(place)">Show Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place info</h1>
        <p>
          Name:
          <input type="text" v-model="updatePlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="updatePlace.address" />
        </p>
        <button v-on:click="placesUpdate(currentPlace)">UPDATE</button>
        <button v-on:click="placesDestroy(currentPlace)">Destroy Place</button>

        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
