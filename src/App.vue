<template>
  
  <div class="head">
  <h1 @click="BackToCatalog()">Online Magazin</h1>
  <input class="marginall" v-model="searchTerm" type="text" />
  <button class="margin" @click="search">Найти &#128270;</button>

  <select class="margin" v-model="selectedFilter" @change="filter">
    <option value="all">Все категории</option>
    <option value="Вверх.одежда">Вверх.Одежда</option>
    <option value="Толстовки">Толстовки</option>
    <option value="Штаны">Штаны</option>
    <option value="Обувь">Обувь</option>
    <option value="Пижама">Пижама</option>
    <option value="Наушники">Наушники</option>
  </select>
  <button class="margin reset" @click="resetFilters">X</button>
  <button @click="openCart()" class="marginCart">Корзина {{ counter }}</button>
</div>
 <div id="catalog">
  <div class="catalogsIMG">
  <div class="catalogs" v-for="product in products" :key="product.id">
  <ul>
    <li>
      <img :src="product.image" :alt="product.name" />
      <h2 class="center">{{ product.name }}</h2>
      <p class="center">{{ product.price }} тнг</p>
      <p class="center">Категория: {{ product.category }}</p>
      <button class="cart" @click="addToCart(product)" >Добавить в корзину</button>
      <button class="details" @click="viewDetails(product)">Посмотреть</button>
    </li>
  </ul>
</div>
</div>
</div>

<div class="detail" id="details" v-if="details" hidden="false">
  
  <img v-bind:src="details.image" v-bind:alt="details.name" />
  <div class="txt">
  <h1 class="text">{{ details.name }}</h1>
  <p class="text">Описание: {{ details.description }}</p>
  <p class="text">Цена: {{ details.price }} тнг</p>
  <p class="text">Дата публикации: {{ details.publicationDate }}</p>
  <button class="marginTop" @click="addToCart(details)">Добавить в корзину</button>
  <button class="marginTop" @click="BackToCatalog()">Назад</button>
</div>
</div>


<div id="cart" hidden> 
  <table>
    <thead>
      <tr>
        <th class="border">Фото товара</th>
        <th class="border">Название товара</th>
        <th class="border">Цена товара</th>
        <th class="border">Кол-во товара</th>
        <th></th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in carts" :key="index">
        <td class="color"><img :src="item.image" alt="item.name" style="height: 120px; width: 90px;"></td>
        <td class="color">{{ item.name }}</td>
        <td class="color">{{ item.price }} тнг</td>
        <td class="color">
          <input type="number" v-model.number="item.quantity" @input="updateTotal()">
        </td>
        <td>
          <button class="margin del" @click="removeFromCarts(index)">Удалить</button>
        </td>
        <td>
          <button class="margin" @click="viewDetails(item)">Посмотреть</button>
        </td>
      </tr>
    </tbody>
  </table>
  <p class="text">Total: {{ total }} тнг</p>
  <button @click="BackToCatalog()"> На главную </button>

</div>



</template>

<script>


export default {
name: 'ProductCatalog',
data() {
return {
  total:0,
  counter: 0,
  allProducts: [
    {
      id: 1,
      name: 'Свит-Шот',
      image: 'https://cdn1.ozone.ru/s3/multimedia-n/6104902403.jpg',
      price: 6000,
      category: 'Вверх.одежда',
      description: 'Свитшот',
      publicationDate: '2022-01-01',
      quantity: 1,
    },
    {
      id: 2,
      name: 'Толстовка',
      image: 'https://avatars.mds.yandex.net/i?id=3d90fd18e88afb5b08265d289056a1e8_l-5356399-images-thumbs&ref=rim&n=13&w=640&h=640',
      price: 10000,
      category: 'Толстовки',
      description: 'Худи оверсайз',
      publicationDate: '2022-01-02',
      quantity: 1,
    },
    {
      id: 3,
      name: 'Рубашка',
      image: 'https://a.lmcdn.ru/product/O/O/OO001EMCTZD7_7269802_1_v1.jpg',
      price: 5000,
      category: 'Вверх.одежда',
      description: 'Рубашка клетчитая',
      publicationDate: '2022-01-03',
      quantity: 1,
    },
    {
      id: 4,
      name: 'Рубашка',
      image: 'http://images.asos-media.com/inv/media/1/9/9/5/8275991/clouddancer/image1xxl.jpg',
      price: 6000,
      category: 'Вверх.одежда',
      description: 'Рубашка клетчетая серая',
      publicationDate: '2022-01-04',
      quantity: 1,
    },
    {
      id: 5,
      name: 'Зипка',
      image: 'https://cdn-images.farfetch-contents.com/17/09/36/09/17093609_34865212_1000.jpg',
      price: 24000,
      category: 'Толстовки',
      description: 'Худи только с застёжкой',
      publicationDate: '2022-01-05',
      quantity: 1,
    },
    {
      id: 6,
      name: 'Шаровары',
      image: 'https://www.muiladot.me/img/products/15964-lonmmy-m-4xl-pli-harem-pantolon-erkek-kot-back-pamuk-ince-skinny-jeans-erkekler-kk-alt-rahat-pantolon-erkek-kot-2017.jpg',
      price: 15000,
      category: 'Штаны',
      description: 'Джинсы',
      publicationDate: '2022-01-06',
      quantity: 1,
    },
    {
      id: 7,
      name: 'Кроссовки Джорданы',
      image: 'https://img.alicdn.com/imgextra/i3/2914266524/TB2fGwjkcj_B1NjSZFHXXaDWpXa_!!2914266524.jpg',
      price: 50000,
      category: 'Обувь',
      description: 'Современные кроссовки',
      publicationDate: '2022-01-07',
      quantity: 1,
    },
    {
      id: 8,
      name: 'Пижама',
      image: 'https://crobux.ru/wp-content/uploads/f/1/d/f1defcd90f81be47f0490d1bad940328.jpeg',
      price: 10000,
      category: 'Пижама',
      description: 'Детская пижама',
      publicationDate: '2022-01-08',
      quantity: 1,
    },
    {
      id: 9,
      name: 'Пижама Единорог',
      image: 'https://ae01.alicdn.com/kf/HTB1lrXQSFXXXXaCXpXXq6xXFXXXY/SAMGU-NEW-Kids-Pink-Blue-Unicorn-onesies-High-Quality-Flannel-costume-Boys-animal-pajamas-pyjama-for.jpg',
      price: 15000,
      category: 'Пижама',
      description: 'Детская пижама единорог',
      publicationDate: '2022-01-09',
      quantity: 1,
    },
    {
      id: 10,
      name: 'Наушники',
      image: 'https://ae01.alicdn.com/kf/HTB1JxoZasrrK1Rjy1zeq6xalFXaG/Kingston-HyperX-Cloud-Stinger-Auriculares-Headphone-Steelseries-Gaming-Headset-with-Microphone-Mic-For-PC-PS4-Xbox.jpg',
      price: 28000,
      category: 'Наушники',
      description: 'Наушники Cloud Stinger',
      publicationDate: '2022-01-10',
      quantity: 1,
    },
    {
      id: 11,
      name: 'Наушники Logitech G435',
      image: 'https://images.fragstore.com/images/detailed/147/logitech-g435-blue-3-1000x1000.jpeg',
      price: 35000,
      category: 'Наушники',
      description: 'Наушники Logitech G435',
      publicationDate: '2022-01-10',
      quantity: 1,
    },
    

  ],
  searchTerm: '',
  selectedFilter: 'all',
  products: [
  {
      id: 1,
      name: 'Свит-Шот',
      image: 'https://cdn1.ozone.ru/s3/multimedia-n/6104902403.jpg',
      price: 6000,
      category: 'Вверх.одежда',
      description: 'Свитшот',
      publicationDate: '2022-01-01',
      quantity: 1,
    },
    {
      id: 2,
      name: 'Толстовка',
      image: 'https://avatars.mds.yandex.net/i?id=3d90fd18e88afb5b08265d289056a1e8_l-5356399-images-thumbs&ref=rim&n=13&w=640&h=640',
      price: 10000,
      category: 'Толстовки',
      description: 'Худи оверсайз',
      publicationDate: '2022-01-02',
      quantity: 1,
    },
    {
      id: 3,
      name: 'Рубашка',
      image: 'https://a.lmcdn.ru/product/O/O/OO001EMCTZD7_7269802_1_v1.jpg',
      price: 5000,
      category: 'Вверх.одежда',
      description: 'Рубашка клетчитая',
      publicationDate: '2022-01-03',
      quantity: 1,
    },
    {
      id: 4,
      name: 'Рубашка',
      image: 'http://images.asos-media.com/inv/media/1/9/9/5/8275991/clouddancer/image1xxl.jpg',
      price: 6000,
      category: 'Вверх.одежда',
      description: 'Рубашка клетчетая серая',
      publicationDate: '2022-01-04',
      quantity: 1,
    },
    {
      id: 5,
      name: 'Зипка',
      image: 'https://cdn-images.farfetch-contents.com/17/09/36/09/17093609_34865212_1000.jpg',
      price: 24000,
      category: 'Толстовки',
      description: 'Худи только с застёжкой',
      publicationDate: '2022-01-05',
      quantity: 1,
    },
    {
      id: 6,
      name: 'Шаровары',
      image: 'https://www.muiladot.me/img/products/15964-lonmmy-m-4xl-pli-harem-pantolon-erkek-kot-back-pamuk-ince-skinny-jeans-erkekler-kk-alt-rahat-pantolon-erkek-kot-2017.jpg',
      price: 15000,
      category: 'Штаны',
      description: 'Джинсы',
      publicationDate: '2022-01-06',
      quantity: 1,
    },
    {
      id: 7,
      name: 'Кроссовки Джорданы',
      image: 'https://img.alicdn.com/imgextra/i3/2914266524/TB2fGwjkcj_B1NjSZFHXXaDWpXa_!!2914266524.jpg',
      price: 50000,
      category: 'Обувь',
      description: 'Современные кроссовки',
      publicationDate: '2022-01-07',
      quantity: 1,
    },
    {
      id: 8,
      name: 'Пижама',
      image: 'https://crobux.ru/wp-content/uploads/f/1/d/f1defcd90f81be47f0490d1bad940328.jpeg',
      price: 10000,
      category: 'Пижама',
      description: 'Детская пижама',
      publicationDate: '2022-01-08',
      quantity: 1,
    },
    {
      id: 9,
      name: 'Пижама Единорог',
      image: 'https://ae01.alicdn.com/kf/HTB1lrXQSFXXXXaCXpXXq6xXFXXXY/SAMGU-NEW-Kids-Pink-Blue-Unicorn-onesies-High-Quality-Flannel-costume-Boys-animal-pajamas-pyjama-for.jpg',
      price: 15000,
      category: 'Пижама',
      description: 'Детская пижама единорог',
      publicationDate: '2022-01-09',
      quantity: 1,
    },
    {
      id: 10,
      name: 'Наушники HyperX Cloud Stinger',
      image: 'https://ae01.alicdn.com/kf/HTB1JxoZasrrK1Rjy1zeq6xalFXaG/Kingston-HyperX-Cloud-Stinger-Auriculares-Headphone-Steelseries-Gaming-Headset-with-Microphone-Mic-For-PC-PS4-Xbox.jpg',
      price: 28000,
      category: 'Наушники',
      description: 'Наушники Cloud Stinger',
      publicationDate: '2022-01-10',
      quantity: 1,
    },
    {
      id: 11,
      name: 'Наушники Logitech G435',
      image: 'https://images.fragstore.com/images/detailed/147/logitech-g435-blue-3-1000x1000.jpeg',
      price: 35000,
      category: 'Наушники',
      description: 'Наушники Logitech G435',
      publicationDate: '2022-01-10',
      quantity: 1,
    },
  ] ,
  details:[],
  carts: [],
}
},
methods: {
search() {
  this.products = this.allProducts 
  this.products = this.products.filter(product =>
    product.name.toLowerCase().includes(this.searchTerm.toLowerCase())
  )
},
filter() {
  this.products = this.allProducts
  if (this.selectedFilter !== 'all') {
    this.products = this.products.filter(product =>
      product.category === this.selectedFilter
    )
  }
},
resetFilters() {
  this.searchTerm = ''
  this.selectedFilter = 'all'
  this.products = this.allProducts
},
viewDetails(product) {
  this.details = product
  document.getElementById("catalog").hidden = true
  document.getElementById("details").hidden = false
  document.getElementById("cart").hidden = true
},
BackToCatalog(){
  document.getElementById("catalog").hidden = false
  document.getElementById("details").hidden = true
  document.getElementById("cart").hidden = true

},
openCart(){
  document.getElementById("catalog").hidden = true
  document.getElementById("details").hidden = true
  document.getElementById("cart").hidden = false


},
addToCart(product){
  if (this.carts.length == 0){  
  this.counter++
  this.carts.push(product)
  this.updateTotal()
  return
  }
  if( this.carts.length > 0){
    for(let i = 0; i < this.carts.length; i++ ){
      if(this.carts[i].id === product.id){alert("Вы уже добавили этот товар") 
      return}
    }
  this.counter++
  this.carts.push(product)
  this.updateTotal()
  }
},
updateTotal() {
    this.total = this.carts.reduce((acc, item) => acc + (item.price * item.quantity), 0)
  },
  removeFromCarts(index) {
    this.carts.splice(index, 1)
    this.updateTotal()
    this.counter--
  },

}
}
</script>
<style>
  img{
    height: 400px;
    width: 300px;
    border-radius: 10px;
  }
  li {
    list-style-type: none; 
   }
   .catalogsIMG{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
   }
   .catalogs{
    width: 300px;
    height: 600px;
   }
   *{
    margin: 0;
    padding: 0;
   }
   .head{
    margin-left: 40% ;
   }
   #catalog{
    margin-top: 30px;
   }
   h1{
    margin-left: 2%;
    font-size: 45px;
    font-family: 'Play', sans-serif;
    color: blueviolet;
   }
   .margin{
    margin-left: 5px;
   }
   .reset{
    background-color: rgba(158, 30, 30, 0.753);
    border: 1px solid rgba(255, 0, 0, 0.705);
    border-radius: 4px;
    width: 12px;
   }
   .reset:hover{
    background-color: rgba(158, 30, 30, 0.37);
   }
   .marginall{
    margin-left:20px;
   }
   .center{
    font-family: 'Play', sans-serif;
    text-align: center;
    color: rgb(78, 10, 141);
   }
   .cart{
    margin-left: 45px;
    margin-top: 5px;
   }
   .details{
    margin-left: 15px;
    margin-top: 5px;
   }
   .marginCart{
    margin-left: 600px;
    width: 100px;
    height: 35px;
    font-size: 20px;
   }
   .detail{
    width: 1000px;
    height: 400px;
    margin-left: 30%;
    margin-top: 10%;
   }
   .txt{
    margin-left: 344px;
    margin-top: -370px;
   }
   .marginTop{
    margin-top: 10px;
    margin-left: 5px;
   }
   .text{
    font-family: 'Play', sans-serif;
    font-size: 20px;
   }
   #cart{
    margin-top: 50px;
    margin-left: 30%;
    width: 60%;

   }
   .border{
    border: 1px solid black;
    width: 180px;
    font-size: 20px;
    border-radius: 5px;
   }
   .color{
    background-color: rgba(187, 184, 184, 0.178);
    border-radius: 5px;
    text-align: center;
   }
   .del{
    background-color: rgba(158, 30, 30, 0.753);
    border: 1px solid rgba(255, 0, 0, 0.705);
    border-radius: 4px;
   }
   .del:hover{
    background-color: rgba(168, 39, 39, 0.253);
   }
</style>