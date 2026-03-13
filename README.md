<!DOCTYPE html><html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Lucky Bee POS 6.2</title><style>
body{
font-family:Arial;
margin:0;
background:#eef2f7;
}

header{
background:#111827;
color:white;
text-align:center;
padding:18px;
font-size:24px;
}

.container{
padding:15px;
}

.card{
background:white;
padding:15px;
border-radius:10px;
margin-bottom:15px;
box-shadow:0 2px 10px rgba(0,0,0,0.15);
}

input{
width:95%;
padding:10px;
margin:6px;
border-radius:6px;
border:1px solid #ccc;
}

button{
padding:10px;
border:none;
border-radius:6px;
color:white;
font-weight:bold;
margin:5px;
}

.loginBtn{background:#2563eb}
.invBtn{background:#2563eb}
.sellBtn{background:#16a34a}
.salesBtn{background:#f59e0b}
.logoutBtn{background:#dc2626}
.backBtn{background:#6b7280}

.products{
display:grid;
grid-template-columns:repeat(2,1fr);
gap:10px;
}

.productBtn{
background:#2563eb;
padding:14px;
border-radius:8px;
}

.checkoutBtn{
background:#16a34a;
width:100%;
font-size:16px;
}

#dashboard,#inventoryPage,#posPage,#salesPage{
display:none;
}
</style></head><body><header>🐝 Lucky Bee POS 6.2</header><div class="container"><div id="loginPage" class="card"><h3>Login</h3><input id="username" placeholder="Username">
<input id="password" type="password" placeholder="Password"><button class="loginBtn" onclick="login()">Login</button>

<p>
Admin: admin / 1234<br>
Cashier: cashier / 1111
</p></div><div id="dashboard" class="card"><h3>Dashboard</h3><button class="invBtn" onclick="showInventory()">Inventory</button>
<button class="sellBtn" onclick="showPOS()">Start Selling</button>
<button class="salesBtn" onclick="showSales()">Sales</button>
<button class="logoutBtn" onclick="logout()">Logout</button>

</div><div id="inventoryPage" class="card"><button class="backBtn" onclick="showDashboard()">Back</button>

<h3>Add Product</h3><input id="pname" placeholder="Product name">
<input id="pprice" placeholder="Price">
<input id="pqty" placeholder="Quantity"><button class="invBtn" onclick="addProduct()">Add Product</button>

<h3>Inventory</h3><div id="inventoryList"></div></div><div id="posPage" class="card"><button class="backBtn" onclick="showDashboard()">Back</button>

<h3>Products</h3><div id="productButtons" class="products"></div><h3>Cart</h3><div id="cartList"></div><h3>Total: K <span id="totalValue">0</span></h3><button class="checkoutBtn" onclick="checkout()">CHECKOUT</button>

</div><div id="salesPage" class="card"><button class="backBtn" onclick="showDashboard()">Back</button>

<h3>Sales History</h3><div id="salesList"></div></div></div><script>

const users={
admin:"1234",
cashier:"1111"
}

let inventory=JSON.parse(localStorage.getItem("inventory"))||[]
let sales=JSON.parse(localStorage.getItem("sales"))||[]
let cart=[]

function saveData(){
localStorage.setItem("inventory",JSON.stringify(inventory))
localStorage.setItem("sales",JSON.stringify(sales))
}

function login(){

let u=document.getElementById("username").value.trim()
let p=document.getElementById("password").value.trim()

if(users[u]===p){

document.getElementById("loginPage").style.display="none"
document.getElementById("dashboard").style.display="block"

loadInventory()
loadProducts()
loadSales()

}else{

alert("Wrong login")

}

}

function logout(){
location.reload()
}

function hideAll(){

document.getElementById("inventoryPage").style.display="none"
document.getElementById("posPage").style.display="none"
document.getElementById("salesPage").style.display="none"
document.getElementById("dashboard").style.display="none"

}

function showDashboard(){

hideAll()
document.getElementById("dashboard").style.display="block"

}

function showInventory(){

hideAll()
document.getElementById("inventoryPage").style.display="block"

}

function showPOS(){

hideAll()
document.getElementById("posPage").style.display="block"

}

function showSales(){

hideAll()
document.getElementById("salesPage").style.display="block"

}

function addProduct(){

let name=document.getElementById("pname").value
let price=parseFloat(document.getElementById("pprice").value)
let qty=parseInt(document.getElementById("pqty").value)

inventory.push({name,price,qty})

saveData()

loadInventory()
loadProducts()

}

function loadInventory(){

let box=document.getElementById("inventoryList")
box.innerHTML=""

inventory.forEach((p,i)=>{

box.innerHTML+=
p.name+" | K"+p.price+" | stock:"+p.qty+
" <button onclick='deleteProduct("+i+")'>Delete</button><br>"

})

}

function deleteProduct(i){

inventory.splice(i,1)

saveData()

loadInventory()
loadProducts()

}

function loadProducts(){

let box=document.getElementById("productButtons")

box.innerHTML=""

inventory.forEach((p,i)=>{

if(p.qty>0){

let btn=document.createElement("button")

btn.className="productBtn"

btn.innerText=p.name+" K"+p.price

btn.onclick=function(){addToCart(i)}

box.appendChild(btn)

}

})

}

function addToCart(i){

let item=inventory[i]

let found=cart.find(c=>c.index===i)

if(found){

found.qty++

}else{

cart.push({index:i,name:item.name,price:item.price,qty:1})

}

renderCart()

}

function renderCart(){

let box=document.getElementById("cartList")

box.innerHTML=""

let total=0

cart.forEach((c,i)=>{

let subtotal=c.price*c.qty
total+=subtotal

box.innerHTML+=
c.name+" x"+c.qty+" K"+subtotal+
" <button onclick='removeItem("+i+")'>X</button><br>"

})

document.getElementById("totalValue").innerText=total

}

function removeItem(i){

cart.splice(i,1)

renderCart()

}

function checkout(){

if(cart.length===0){
alert("Cart empty")
return
}

let total=0

cart.forEach(item=>{

inventory[item.index].qty-=item.qty

total+=item.price*item.qty

})

sales.push("Sale K"+total)

cart=[]

saveData()

renderCart()
loadProducts()
loadSales()

alert("Sale completed")

}

function loadSales(){

let box=document.getElementById("salesList")

box.innerHTML=""

sales.forEach(s=>{

box.innerHTML+=s+"<br>"

})

}

</script></body>
</html>
