<template>
  <router-view :version="version" :statPath="statPath" />
  <v-tour v-if="tutorial" ref="tour" id="vue-tour" name="myTour" :steps="steps" :options="tour_options"
    :callbacks="myCallbacks">
  </v-tour>


  <!-- 
  Footer
  -->

  <!-- TODO Footer removed -->
  <!-- <Footer 
    :version="version" /> -->
</template>
<script>
import Footer from './components/Footer.vue'
import axios from "axios"; //ajax
export default {
  name: 'App',
  components: {
    Footer,
  },

  data() {
    return {
      myCallbacks: {
        onSkip: this.skipTutorial,
        onFinish: this.skipTutorial,
      },
      tutorial: true,
      tour_options: {
        useKeyboardNavigation: false,
      },
      test: [false, true],
      steps: [
        {
          target: '[data-v-step="sim"]',
          content: "Simulate and visualize the blood-sugar levels of the patient",
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="params"]',
          content: "Control the External Factors, Treatment and Physiological parameters of the patient",
          params: {
            placement: 'right',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="patients"]',
          content: "Choose from a number of pretuned patient files...",
          params: {
            placement: 'top',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="upload-patients"]',
          content: "...Or import/export your own configurations.",
          params: {
            placement: 'top',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="save-response"]',
          content: "Save the current simulation for later use.",
          params: {
            placement: 'right',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="plot-against"]',
          content: "Plot a simulation against previous simulations",
          params: {
            placement: 'bottom',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="response-stats"]',
          content: "View the response statistics of each simulation",
          params: {
            placement: 'top',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="plotting-options"]',
          content: "Plot multiple states at the same time",
          params: {
            placement: 'left',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="simtime"]',
          content: "Change the simulation period.",
          params: {
            placement: 'bottom',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="zoom"]',
          content: "Zoom to the last n days.",
          params: {
            placement: 'bottom',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="advanced"]',
          content: "Adjust advanced model parameters",
          params: {
            placement: 'bottom',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="summary"]',
          content: "View a summary of parameters for each simulation",
          params: {
            placement: 'bottom',
            enableScrolling: false

          }
        },
        {
          target: '[data-v-step="png"]',
          content: "Save graph PNG",
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="save"]',
          content: "Save simulation response data as a csv file",
          params: {
            placement: 'bottom',
            enableScrolling: false

          }
        },
      ],
      version: "v0.246",
      statPath: "https://dev.t2d.aau.dk/php/" // https://dev.t2d.aau.dk/php/ , https://t2d.aau.dk/php/
    }
  },
  mounted() {
    if (this.tutorial) {
      this.$tours['myTour'].start()
    }

  },
  created() {
    var cookie = $cookies.get('tutorial');
    if (cookie != null) { // Check if cookie exists
      if (cookie.tutorial == true) { // Check if EULA is consented
        this.tutorial = false;
      }
      if (cookie.key == "") { // Check if a cookie-key is set, else request one.
        axios.get(this.statPath + "NewUser.php")
          .then(response => {
            cookie.key = response.data; // Grabs the cookie-id sent from the server
            $cookies.set('tutorial', cookie, 'y') // Updates the saved cookie
          })
          .catch(err => {
            // Manage the state of the application if the request 
            // has failed      
          })
      }
      else { // If the ID is set, we want to register it as a visit (flag 0)
        axios.post(this.statPath + "Visit.php", JSON.stringify({
          CookieID: cookie.key,
          flag: 0,
        }))
          .then(response => {
            //console.log(response)
          })
          .catch(err => {
            // Manage the state of the application if the request 
            // has failed      
          })
      }
    }
  },
  methods: {
    skipTutorial() {
      // Generates cookie
      var cookie = {
        tutorial: true,
        Version: this.version,
        key: ''
      }
      $cookies.set('tutorial', cookie, 'y')
      // Removes EULA pop-up
      this.Tutorial = false;
      // Asks server for a unique cookie id (Async so user can access the site if the server is unaccessable)
      axios.get(this.statPath + "NewUser.php")
        .then(response => {
          cookie.key = response.data; // Grabs the cookie-id sent from the server
          $cookies.set('tutorial', cookie, 'y') // Updates the saved cookie
        })
        .catch(err => {
          // Manage the state of the application if the request 
          // has failed      
        })


    },
  }
}
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.v-step__button-next {}

#vue-tour {
  position: absolute;
  width: 400px;

  z-index: 5;


}

.v-step__button-next {
  position: absolute;
  right: 10px;
  bottom: 10px;
  padding-top: 20px;
}


.hide_next .v-step__button-next {
  display: none !important;
}

.v-step__button-skip {
  position: absolute;
  left: 10px;
  bottom: 10px;
  border: none !important;
}

.v-step__button-previous {
  display: none !important;
}

.v-step__content {
  padding-bottom: 20px !important;
}

.v-step {
  background: #22234E !important;


}
</style>

<!-- steps2: [
        {

          hide_next: true,
          target: '[data-v-step="sim"]',
          content: "Let us start by simulating the patients blood-sugar during the day.",
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },
        {
          hide_next: false,
          target: '[data-v-step="graph"]',
          content: "Great job! The patient receives three meals a day. Notice how each meal causes the blood-sugar to rise.",
          params: {
            placement: 'bottom-end',
            enableScrolling: false

          }
        },
        {
          hide_next: false,
          target: '[data-v-step="External factors"]',
          content: "The External Factors section controls the factors that the patient is exposed to.",
          params: {
            placement: 'right',
            enableScrolling: false

          }
        },
        {
          hide_next: true,
          target: '[data-v-step="Physical Activity"]',
          content: "Meals have are already been added, but let's add some Physical Activity",
          params: {
            placement: 'right',
            enableScrolling: false


          }
        },
        {
          hide_next: false,
          target: '[data-v-step="Physical Activity"]',
          content: "Add a daily Physical Activity from 14:00 - 16:00.",
          params: {
            placement: 'right',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="sim"]',
          content: 'Great, now press simulate again to see the effect',
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="graph"]',
          content: 'In contrast to the Meals the Physical Activity causes the blood sugar to drop.',
          params: {
            placement: 'bottom-end',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="plot-against"]',
          content: 'In order to see the difference more clearly, plot the current simulation against the previous one.',
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },
        {
          hide_next: false,
          target: '[data-v-step="Treatment"]',
          content: 'The Treatment section allows us to expose the patient to different treatments.',
          params: {
            placement: 'right',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="Fast Acting Insulin"]',
          content: "Let's add some Fast Acting Insulin",
          params: {
            placement: 'right',
            enableScrolling: false
          }
        },
        {

          target: '[data-v-step="Fast Acting Insulin"]',
          content: 'Add an arbitrary dose at 12:00',
          params: {
            placement: 'right',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="simtime"]',
          content: 'Change the simulation period to 3 days.',
          params: {
            placement: 'bottom',
            enableScrolling: false

          }
        },
        {
          hide_next: true,
          target: '[data-v-step="sim"]',
          content: 'Perfect, now resimulate!',
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="save-response"]',
          content: "Very good! Save the current simulation by giving it a name and clicking the 'Save' icon.",
          params: {
            placement: 'left',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="response-stats"]',
          content: "The response statistics summarize the values of each simulation.",
          params: {
            placement: 'top',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="plotting-options"]',
          content: "In order to plot more than just blood-sugar, open 'Plotting Options'.",
          params: {
            placement: 'top',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="plotting-options"]',
          content: "Plot blood insulin on the 2. Y-axis.",
          params: {
            placement: 'top',
            enableScrolling: false
          }
        },
        {
          hide_next: true,
          target: '[data-v-step="states_menu"]',
          content: "Nice! Close the dropdown menu by clicking outside of it",
          params: {
            placement: 'left',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="graph"]',
          content: "Great job! Notice how the blood sugar and insulin levels follow eachother.",
          params: {
            placement: 'left',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="patients"]',
          content: "A number of pretuned patients can be found in the 'Patients' tab.",
          params: {
            placement: 'top',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="upload-patients"]',
          content: "But it is also possible to upload and download patient files.",
          params: {
            placement: 'top',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="png"]',
          content: "You can download the graph as a PNG file",
          params: {
            placement: 'left',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="save"]',
          content: "Or download a csv file with the simulation response data, by clicking the 'Download' icon.",
          params: {
            placement: 'left',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="advanced"]',
          content: "Advanced Model parameters can be adjusted in the 'Advanced' section.",
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },
        {
          target: '[data-v-step="summary"]',
          content: "Use the summary tab to keep track of the parameters from each simulation.",
          params: {
            placement: 'bottom',
            enableScrolling: false
          }
        },

      ], -->