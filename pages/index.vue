<template>
  <div class="container">
    <br />
    
    <div class="row">
      <div class="col">
        <h2>เพิ่มรายการสินค้า</h2>
        <br />
        <b-form @submit="onSubmit" @reset="onReset">
          <b-form-input
            v-model="name"
            placeholder="ชื่อสินค้า"
            :state="!errors.price"
            @change="validate"
          ></b-form-input>
          <b-form-invalid-feedback :state="!errors.name">ชื่อไม่ถูกต้อง</b-form-invalid-feedback>
          <b-form-valid-feedback :state="!errors.name">ถูกต้อง</b-form-valid-feedback>
          <br />
          <b-form-input
            v-model="price"
            type="number"
            :state="!errors.price"
            placeholder="ราคา"
            @change="validate"
          ></b-form-input>
          <b-form-invalid-feedback :state="!errors.price">ราคาต่ำเกินไป</b-form-invalid-feedback>
          <b-form-valid-feedback :state="!errors.price">ถูกต้อง</b-form-valid-feedback>
          <br />
          <p>สรุปข้อมูลเพิ่มสินค้า</p>
          <div class="mt-2">: {{ name }}</div>
          <div class="mt-2">: {{ price }}</div>
          <br />
          <b-button type="submit" variant="success">เพิ่มสินค้า</b-button>
          <b-button type="reset">ยกเลิก</b-button>
        </b-form>
      </div>
      <div class="col">
        <b>แสดงรายการสินค้า</b>
        <ul>
          <li v-for="(product, index) in products" :key="index">
            {{ product.name }} {{ product.price }} บาท
            <span>
              <b-button variant="outline-primary" size="sm" @click="addOrder(product)">เลือก</b-button>
            </span>
          </li>
        </ul>
      </div>
      <div class="col">
        <b>เลือกซื้อสินค้า</b>
        <ul>
          <li v-for="(order, index) in orders" :key="index">
            {{ order.name }} จำนวน
            <span>
              <b-button size="sm" @click="subtractOrderProduct(order)">-</b-button>
            </span>
            {{ order.count }}
            <span>
              <b-button size="sm" @click="addOrderProduct(order)">+</b-button>
            </span>
            = {{ order.price * order.count }} บาท
          </li>
        </ul>
        <div>
          <p>
            รวม
            <b>{{ totalPrice }}</b> บาท
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Logo from "~/components/Logo.vue";

export default {
  components: {
    Logo
  },
  data() {
    return {
      name: "",
      price: 0,
      errors: {
        name: false,
        price: true
      },
      products: [
        {
          name: "ไวท์มอลล์",
          price: 30
        },
        {
          name: "ชาไทย",
          price: 25
        }
      ],
      orders: []
    };
  },
  methods: {
    onSubmit(evt) {
      evt.preventDefault();
      if (!this.validate()) return;
      this.products.push({
        name: this.name,
        price: Number(this.price)
      });
      this.onReset(evt);
    },
    onReset(evt) {
      evt.preventDefault();
      this.name = "";
      this.price = 0;
    },
    validate() {
      this.errors.price = this.price <= 0;
      if (this.errors.price) return false;

      return true;
    },
    addOrder(order) {
      const foundOrder = this.orders.find(_order => _order.name === order.name);
      if (!foundOrder) {
        this.orders.push({
          name: order.name,
          count: 1,
          price: order.price
        });
      } else {
        foundOrder.count += 1;
      }
    },
    subtractOrderProduct(order) {
      order.count -= 1;
      this.orders = this.orders.filter(_order => _order.count !== 0);
    },
    addOrderProduct(order) {
      order.count += 1;
    }
  },
  computed: {
    totalPrice() {
      return this.orders.reduce((total, order) => {
        return (total += order.price * order.count);
      }, 0);
    }
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>