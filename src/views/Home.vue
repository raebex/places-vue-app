<template>
  <div class="home">
    <h2>Add a place</h2>
    Name: <input type="text" v-model="newName">
    <br>
    Address: <input type="text" v-model="newAddress">
    <br>
    <div v-for="error in errors" :key="error">
      <p>{{ error }}</p>
    </div>
    <button v-on:click="createPlace">Create</button>

    <ul v-for="place in places" :key="place.id">
      <li>
        <h2>{{ place.name }}</h2>
        <p>{{ place.address }}</p>
        <button v-on:click="showPlace(place)">More Info</button>
      </li>
    </ul>

    <dialog>
      <form method="dialog">
        <h3>Place Info</h3>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>
<style></style>
<script>
import axios from "axios";

export default {
  data: function() {
    return {
      places: [],
      errors: [],
      newName: "",
      newAddress: "",
      currentPlace: {},
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function() {
      axios.get("/api/places").then(response => {
        this.places = response.data;
      });
    },
    createPlace: function() {
      var params = {
        name: this.newName,
        address: this.newAddress,
      };

      axios
        .post("/api/places", params)
        .then(response => {
          this.places.push(response.data);
          this.newName = "";
          this.newAddress = "";
          this.errors = [];
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function(inputPlace) {
      this.currentPlace = inputPlace;
      document.querySelector("dialog").showModal();
    },
    updatePlace: function(inputPlace) {
      var params = {
        name: inputPlace.name,
        address: inputPlace.address,
      }
      axios
        .patch(`/api/places/${inputPlace.id}`, params)
        .then(response => {
          console.log("Success!", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function(inputPlace) {
      axios
        .delete(`/api/places/${inputPlace.id}`)
        .then(response => {
          console.log(response.data);
          var index = this.places.indexOf(inputPlace);
          this.places.splice(index, 1);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });
    },
  },
};
</script>