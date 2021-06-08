<template>
  <div class="home">
    <div>
      <h2>Create:</h2>
      <input v-model="newPlaceParams.name" type="text" />
      <br />
      <input v-model="newPlaceParams.address" type="text" />
      <br />
      <button v-on:click="createPlace()">Submit</button>
    </div>
    <br />
    <div v-for="place in places" v-bind:key="place.id">
      <p>
        Name:
        {{ place.name }}
      </p>
      <p>
        Address:
        {{ place.address }}
      </p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <div>
          <h1>Information for: {{ currentPlace.name }}</h1>
          <p>
            Name:
            <input type="text" v-model="currentPlace.name" />
          </p>
          <p>
            Address:
            <input type="text" v-model="currentPlace.address" />
          </p>

          <div>
            <button v-on:click="updatePlace()">Update</button>
            <button>Close</button>
          </div>

          <br />
        </div>
        <div>
          <button v-on:click="destroyPlace()">Destroy</button>
        </div>
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
      places: [],
      newPlaceParams: {},
      currentPlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places/").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      axios
        .post("http://localhost:3000/places/", this.newPlaceParams)
        .then((response) => {
          console.log("Success!", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function () {
      var updateParams = this.currentPlace;
      axios
        .patch(`http://localhost:3000/places/${this.currentPlace.id}`, updateParams)
        .then((response) => {
          console.log("Success!", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function () {
      axios.delete(`http://localhost:3000/places/${this.currentPlace.id}`).then((response) => {
        console.log("Success!", response.data);
        var index = this.places.indexOf(this.currentPlace);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
