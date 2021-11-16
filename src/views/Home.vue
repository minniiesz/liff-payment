<template>
  <div class="home">
    <div><h1>UserId : {{profile.userId}}</h1></div>
    <br><br>
    <div>
     <!-- <button @click="getProfile()"><h1>Get UserId</h1></button> -->
        <div v-for="product in carts.products" v-bind:key="product"> 
          <h2>name: {{product.title}} quantity: {{product.quantity}} price: {{product.quantity*product.price}}</h2>
        </div>   
      </div>
    <div >
    <div  class="center">
      <button  id="pay" type="button" disabled @click.prevent="checkout()"><h1>Pay</h1></button>
     
    <form action="checkout.php" id="my-custom-checkout-form" method="POST">
      <input type="hidden" :value="carts.totalPrice" name="total">
      <input type="hidden" value="" name="omiseToken">  

    </form>
    </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { initializeApp } from 'firebase/app';
import { getFirestore, collection, getDocs, doc } from 'firebase/firestore/lite';

  const firebaseConfig = {
    apiKey: "AIzaSyDRsmN8La18FGtYyMWEYoBhX1Tje-sA-Jk",
    authDomain: "getnamefirestore-pn9d.firebaseapp.com",
    projectId: "getnamefirestore-pn9d",
    storageBucket: "getnamefirestore-pn9d.appspot.com",
    messagingSenderId: "900710855909",
    appId: "1:900710855909:web:3e004e40583948330e900c",
    measurementId: "G-LRDZQPFBQH"
  };
  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);
  
  async function getCarts(db) {
  const cartsCol = collection(db, 'carts');
  const cartsSnapshot = await getDocs(cartsCol);
  const cartsList = cartsSnapshot.docs.map(doc => doc.data());

  return cartsList;
}


export default {
  name: 'Home',
 data:() => ({
    carts:[],
    profile:[]
  
}),
  beforeCreate () {
    liff.init({ liffId: "1656343498-yxPX147d" }, () => {
    if (liff.isLoggedIn()) {
    liff.getProfile()
      .then(profile => {
       this.profile = profile
    getCarts(db).then(doc => 
    {      
      for(let cart of doc){
       //console.log(cart.userId);
        if(cart.userId == profile.userId){
          const data = {
            'products' : cart.products,
            'totalPrice': cart.totalPrice,
            'totalQuantity': cart.totalQuantity,
            'userId': cart.userId
          }
        this.carts = data ; 
        document.getElementById("pay").disabled = false;
       //console.log(typeof this.carts.totalPrice);
        }
      }
    });
      }).catch((err) => {
  console.log('error', err);
});
    } else {
    liff.login();
    }
  });
  },
created() {  


},
  methods: {
     checkout() {
      OmiseCard.open({
        frameLabel: "Merchant",
        submitLabel: "PAY RIGHT NOW !",
        amount: this.carts.totalPrice*100
      })
    },
    getProfile(){
       let _this = this
      this.$liff.getProfile().then(function (profile) {
        _this.profile = profile
      }).catch(function (error) {
        alert('Error getting profile: ' + error)
      })
    }


},

  mounted() {
    OmiseCard.configure({
      publicKey: "pkey_test_5ou38wt4nuuigbj90tg",
      image: "https://cdn.omise.co/assets/dashboard/images/omise-logo.png",
      currency: "THB",
      onCreateTokenSuccess: token => {
        const form = document.querySelector("#my-custom-checkout-form")
        form.omiseToken.value = token;
        form.submit()
      }
    })
    OmiseCard.attach()
  }
}

// export const loadFirestore = () => {
//   return(cart) => {
//     Firestore
//       .collection("carts")
//       .get()
//       .then((querySnapshot) =>{
//           if (!querySnapshot.empty) {
//           querySnapshot.forEach((doc) => {
//             console.log(doc.id, "=>", doc.data());
//           });
//       }
//       })
//       .catch((error)=>{
//       console.log(error);
//       });

          
//   }
// }
</script>


<style>
.container {
  height: 200px;
  position: relative;
  border: 3px solid green;
}

.vertical-center {
  margin: 0;
  position: absolute;
  top: 50%;
  -ms-transform: translateY(-50%);
  transform: translateY(-50%);
}
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
  
}
</style>
