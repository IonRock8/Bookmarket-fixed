<template>
  <v-app>
    <v-content>
      <router-view></router-view>
      
      <div v-if="orders.length > 0">
        <v-card><h2>รายการสั่งซื้อ</h2></v-card>
        <table>
          <thead>
            <tr>
              <th>รหัสการสั่งซื้อ</th>
              <th>ชื่อผู้สั่งซื้อ</th>
              <th>จำนวนเงินทั้งหมด(บาท)</th>
              <th>เบอร์โทร</th>
              <th>วิธีการชำระ</th>
              <th>จัดการ</th>
              </tr>
          </thead>
          <tbody>
            <tr v-for="(order, index) in orders" :key="index">
              <td>{{ order.id }}</td>
              <td>{{ order.data().name }}</td>
              <td>{{ order.data().totalPrice }}</td>
              <td>{{ order.data().telephone }}</td>
              <td>{{ order.data().paymentMethod }}</td>
              <td>
                <v-btn color="red" class="white--text" @click="deleteOrder(order.id)">ลบ</v-btn>
              </td>
              </tr>
          </tbody>
        </table>
      </div>
      <div v-else>
        <h2>ไม่พบรายการสั่งซื้อ</h2>
      </div> 
      <v-card-actions class="d-flex justify-center">
                <v-btn  x-large to="/">กลับไปหน้าหลัก</v-btn>
      </v-card-actions>
    </v-content>
  </v-app>
</template>

<script>
import firebase from "firebase/app";
import "firebase/auth";
import "firebase/firestore";

export default {
  name: "App",
  data() {
    return {
      orders: []
    };
  

  },
  mounted() {

    // Retrieve orders from Firestore
    this.fetchOrders();
  },
  methods: {
    fetchOrders() {
      firebase
        .firestore()
        .collection("order")
        .get()
        .then(querySnapshot => {
          querySnapshot.forEach(doc => {
            this.orders.push(doc);
          });
        })
        .catch(error => {
          console.error("Error fetching orders: ", error);
        });
    },
    deleteOrder(orderId) {
    firebase
      .firestore()
      .collection("order")
      .doc(orderId)
      .delete()
      .then(() => {
        console.log("Order deleted successfully:", orderId);
        this.orders = this.orders.filter(order => order.id !== orderId);
      })
      .catch(error => {
        console.error("Error deleting order:", error);
      });
  }
  }
};
</script>
<style lang="scss" scoped>
.v-application {
  font-family: "Kanit";
}
h2{
  text-align: center;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin: 0 auto;
  align-content: center;
}

th, td {
  padding: 8px;
  border: 1px solid #dddddd;
  text-align: left;
}

/* เพิ่มระยะห่างของช่อง */
td {
  padding-top: 12px;
  padding-bottom: 12px;
}
/* เพิ่มระยะห่างระหว่าง table กับ v-card */
.v-card {
  margin-top: 20px; /* เพิ่มระยะห่างด้านบน */
  margin-bottom: 20px; /* เพิ่มระยะห่างด้านล่าง */
}

</style>