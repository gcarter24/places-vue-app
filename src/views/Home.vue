<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <p>
      Name:
      <input type="text" v-model="newPlaceName" placeholder="Enter Name" />
    </p>
    <p>
      Address:
      <input type="text" v-model="newPlaceAddress" placeholder="Enter Address" />
    </p>
    <button v-on:click="createPlace">Add Place</button>
    <div v-for="place in places" v-bind:key="place.id">
      <ul>
        <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
      <p>{{ place.name }}</p>
      <p><button v-on:click="showPlace(place)">More Info</button></p>
      <hr />
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button>Close</button>
        <button v-on:click="destroyPlace(currentPlace)">Remove</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to the Places App!",
      errors: [],
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      console.log("index");
      axios.get("http://localhost:3000/api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios.post("http://localhost:3000/api/places", params).then((response) => {
        console.log(response.data);
        this.places.push(response.data);
        this.newPlaceName = "";
        this.newPlaceAddress = "";
      });
    },
    showPlace: function (thePlace) {
      console.log(thePlace);
      this.currentPlace = thePlace;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (thePlace) {
      console.log(thePlace);
      var params = {
        name: thePlace.name,
        address: thePlace.address,
      };
      axios.patch("/api/places/" + thePlace.id, params).then((response) => {
        console.log(response.data);
      });
    },
    destroyPlace: function (thePlace) {
      console.log(thePlace);
      axios.delete("/api/places/" + thePlace.id).then((response) => {
        console.log(response.data);
        var index = this.places.indexOf(thePlace);
        this.places.splice(index, 1);
      });
    },
    submit: function () {
      var params = {
        title: this.newTitle,
      };
      axios
        .post("/api/recipes", params)
        .then((response) => {
          console.log("Success", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
