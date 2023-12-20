<template>
    <div>
        <!-- <v-container>
            <v-row>
      
                <v-col>
                    <showMap :points="points"></showMap>
                </v-col>
                
            </v-row>
        </v-container> -->
        
        <v-container class='accent'>
            <v-btn @click="showOrder()"> Create Order </v-btn>
            <v-divider class="mt-6"></v-divider>
      
        <v-row v-for="order in deliveries"  > 
            <v-col class="pt-2">
                <delivery :address="order.address" :deliveryTime="order.time" :driver="order.driver" class="mt-4" ></delivery>
                <showMap :points="points" :latitude="order.lat" :longitude="order.long"></showMap>
                <!-- <v-btn @click="editDriver(order)"> Edit Driver </v-btn> -->
                <v-btn-toggle
        v-model="order.driver"
        tile
        color="deep-purple accent-3"
        group
      >
        <v-btn value="jake" @click="updateDriver('jake', order.id)">
          Jake
        </v-btn>

        <v-btn value="jose" @click="updateDriver('jose', order.id)">
          Jose
        </v-btn>

        <v-btn value="marisol" @click="updateDriver('marisol', order.id)">
          Marisol
        </v-btn>

        <v-btn value="juanita" @click="updateDriver('juanita', order.id)">
          Juanita
        </v-btn>
      </v-btn-toggle>
      <v-btn @click="orderDelivered(order)"> Delivered</v-btn>
                <v-divider class="mt-6"></v-divider>
            </v-col>
        </v-row>
      
      
    </v-container>
    <v-overlay v-if="overlay">
        <v-container class="secondary">
            <v-row>
                <v-col>
                    <v-text-field
            v-model="inputAddress"
            label="Address"
          ></v-text-field>
                </v-col>
            </v-row>
            <v-row>
                <v-col>
                    <v-text-field
            v-model="inputTime"
            label="Time"
          ></v-text-field>
                </v-col>
            </v-row>
            <v-row>
                <v-col>
                    <v-text-field
            v-model="inputLat"
            label="Lat"
          ></v-text-field>
                </v-col>
            </v-row>
            <v-row>
                <v-col>
                    <v-text-field
            v-model="inputLong"
            label="Long"
          ></v-text-field>
                </v-col>
            </v-row>
            <v-row>
                <v-col>
                    <v-btn @click="cancel()">
                        Cancel
                    </v-btn>
                </v-col>
                <v-col>
                    <v-btn @click="createOrder()">
                        Submit
                    </v-btn>
                </v-col>
            </v-row>
        </v-container>
    </v-overlay>
    <v-overlay v-if="editDriver">
        <v-container>
            <v-row>
                <v-col>
                    <v-text-field
            v-model="inputDriver"
            label="Driver"
          ></v-text-field>
                </v-col>
            </v-row>
            <v-row>
                <v-col>
                    <v-btn @click=cancelEdit()>
                        Cancel
                    </v-btn>
                </v-col>
                <v-col>
                    <v-btn>
                        Edit
                    </v-btn>
                </v-col>
            </v-row>
        </v-container>
    </v-overlay>
    
    </div>
    </template>
    
    
    <script>
    
    export default {
      data() {
        return {
            inputAddress: '',
            inputTime: '',
            inputDriver: '',
            inputLat: '',
            inputLong: '',
            editDriver: false,
            overlay: false,
            points: [{"lat": 29.7612, "long": -95.4612 },],
            address: "this address",
            deliveryTime: "This time",
            driver: "This Driver",
            orders: {
            },
            deliveries: {},
    
    }
    
    
    },
      props: {
        //the parameters the comopnet accepst
        // message: String,
        // product: Object,
        // email: {
          // type: String
          // required: true,
          // default: 'none'
          // validator: function (value) {
          //   return true if valid
    //       String
    // Number
    // Boolean
    // Array
    // Object
    // Date
    // Function
    // Symbol
          // }
      },
      computed: {
        // fullName: function () {
        //   return this.fistName + ' ' + this.lastName
      },
      watch: {
        //called when firstname changes value
        // firstName: function (value, oldValue) {...}
      },
    
    
      methods: {
        async updateDriver(name, id) {
          var updateSchema = {name: name, id: id}
          const response = await this.$axios.$put('/api/updateDriver', updateSchema)
        },
        async getDeliveries() {
          const response = await this.$axios.$get('/api/deliveries')
          this.deliveries = response
          console.log("deliveries")
          console.log(this.deliveries)
        },

        async createDelivery(deliveryObj){
          const response = await this.$axios.$post('/api/delivery', deliveryObj)
          console.log(response)
        },


        cancelEdit(){
            this.editDriver = false
        },
        
        showOrder(){
            this.overlay = true
        },
        cancel(){
            this.overlay = false
        },
        createOrder(){
            this.overlay = false
            console.log(Object.keys(this.orders).length)
            var obj = { address: this.inputAddress, deliveryTime: this.inputTime, driver: "jake", lat: this.inputLat, long: this.inputLong}
            var deliveryObj = { address: this.inputAddress, time: this.inputTime, driver: "jake", lat: this.inputLat, long: this.inputLong}
            this.createDelivery(deliveryObj)
            console.log("HIHIHIHIHIHIHIHIHIHIHIHIHIH")
            this.orders[String(Object.keys(this.orders).length + 1)] = obj
            var coords = {"lat": Number(this.inputLat), "long": Number(this.inputLong)}
            this.points.push(coords)
            console.log(this.orders)
            console.log(this.points)
            this.$forceUpdate();
        },
        async orderDelivered(order){
          console.log(order.id)
          console.log(typeof order.id)
            var updateSchema = { "id": order.id}
            console.log(updateSchema)
            const response = await this.$axios.$put('/api/deleteDelivery', updateSchema )

            // console.log("What Is order? ")
            // console.log(order)
            // for (const [key,value] of Object.entries(this.orders)){
            //     console.log(key,value)
            //     console.log("helper")
            //     console.log(order)
            //     if (value == order){
            //         console.log("OMG IT worked")
            //         console.log(this.orders)
            //         delete this.orders[key]
            //         console.log(this.orders)
            //         this.$forceUpdate()
            //     }
            // }
            
        },
        editDriveree(order){
            this.editDriver = true
        },
    // fetching Data
        // async grabBCData() {
        //   const response = await this.$axios.$get('/api/readAllBusCarts');
        //   this.bcData = response
        //
        //   const startingBay = '202'
        //   this.changeBayParent(startingBay)
        // }
    
    },
    //Lifecycle Hooks: beforeCreate, created, beforeMount, mounted, beforeUPdate, updated, beforeDestroy, destroyed
    mounted() {
      console.log("MountedBaby");
      this.getDeliveries();
    
      console.log("CalledThatData")
    
    
    }
    
    };
    
    </script>
    
    <style scoped>
    
     /* <v-col class="indigo">
                <v-row>
                  <v-col>
                    <v-icon @click="clickedNum(1)"> mdi-numeric-1-box-outline </v-icon>
                  </v-col>
                  <v-col>
                    <v-icon @click="clickedNum(2)"> mdi-numeric-2-box-outline </v-icon>
                  </v-col>
                  <v-col>
                    <v-icon  @click="clickedNum(3)"> mdi-numeric-3-box-outline </v-icon>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-icon @click="clickedNum(4)"> mdi-numeric-4-box-outline </v-icon>
                  </v-col>
                  <v-col>
                    <v-icon @click="clickedNum(5)"> mdi-numeric-5-box-outline </v-icon>
                  </v-col>
                  <v-col>
                    <v-icon @click="clickedNum(6)"> mdi-numeric-6-box-outline </v-icon>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-icon @click="clickedNum(7)"> mdi-numeric-7-box-outline </v-icon>
                  </v-col>
                  <v-col>
                    <v-icon @click="clickedNum(8)"> mdi-numeric-8-box-outline </v-icon>
                  </v-col>
                  <v-col>
                    <v-icon @click="clickedNum(9)"> mdi-numeric-9-box-outline </v-icon>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    
                  </v-col>
                  <v-col>
                    <v-icon @click="clickedNum(0)"> mdi-numeric-0-box-outline </v-icon>
                  </v-col>
                  <v-col>
                    <v-icon @click="clickedNum('a')"> mdi-backspace-outline</v-icon>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-text-field v-model="bayNum"  > </v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-btn class="mt-6" @click="runnerUp()">
                      Runner Up!
                    </v-btn>
                  </v-col>
                </v-row>
             
              </v-col> */
    /*
    vuetify styling
    
    ranges from 0-16
    {property} {direction} -{size}
    property is type of spacing, m for margin p for padding
    direction designates the side the property applies to
    t (top)
    b (bottom)
    l (left)
    r (right)
    x (left and right )
    y (top and bottom)
    a (all directions)
    size (1-16)
    1 is 4px, 2 is 8 px, n1 is -4 px or auto
    
    with breakpoints for screen sizes, xs, sm, md, lg, xl
    
    {property}{direction}-{breakpoint}-{size} */
    
    
    </style>
    
    
    
    
    <!-- for axios stuff
    
    new Vue({
      el: '#app',
      data () {
        return {
          info: null
        }
      },
      mounted () {
        axios
          .get('https://api.coindesk.com/v1/bpi/currentprice.json')
          .then(response => (this.info = response))
      }
    }) -->