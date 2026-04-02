# shopsmarter-website
Price comparison tool prototype
<!DOCTYPE html>
<html>
<head>
  <title>SmartShop Advanced</title>
  <style>
    body { font-family: Arial; background: #f4f4f4; text-align: center; }
    .container { background: white; padding: 20px; margin: auto; width: 90%; border-radius: 10px; }
    input { margin: 5px; padding: 8px; }
    button { padding: 10px; background: green; color: white; border: none; }
    table { margin-top: 20px; width: 100%; border-collapse: collapse; }
    th, td { border: 1px solid #ddd; padding: 10px; }
    .best { background-color: lightgreen; }
    .warning { color: red; font-weight: bold; }
  </style>
</head>

<body>

<h1>🛒 SmartShop Advanced Comparison Tool</h1>

<div class="container">

  <h3>Add Product</h3>

  <input id="store" placeholder="Store">
  <input id="price" type="number" placeholder="Original Price">
  <input id="discount" type="number" placeholder="Discount %">
  <input id="quantity" type="number" placeholder="Quantity">
  <input id="shipping" type="number" placeholder="Shipping Fee">
  <input id="rating" type="number" placeholder="Rating (1-5)">

  <br><br>
  <button onclick="addProduct()">Add Product</button>

  <table id="table">
    <tr>
      <th>Store</th>
      <th>Final Price</th>
      <th>Unit Price</th>
      <th>Rating</th>
      <th>Value Score</th>
      <th>Notes</th>
    </tr>
  </table>

  <h3 id="recommendation"></h3>
  <h3 id="savings"></h3>

</div>

<script>
let products = [];

function addProduct() {
  let store = document.getElementById("store").value;
  let price = parseFloat(document.getElementById("price").value);
  let discount = parseFloat(document.getElementById("discount").value);
  let quantity = parseFloat(document.getElementById("quantity").value);
  let shipping = parseFloat(document.getElementById("shipping").value);
  let rating = parseFloat(document.getElementById("rating").value);

  let finalPrice = price - (price * discount / 100) + shipping;
  let unitPrice = finalPrice / quantity;
  let valueScore = rating / unitPrice;

  let notes = "";
  if (shipping > 0) notes += "⚠ Hidden cost ";
  if (rating < 3) notes += "⚠ Low rating ";

  products.push({ store, finalPrice, unitPrice, rating, valueScore, notes });

  display();
}

function display() {
  let table = document.getElementById("table");

  table.innerHTML = `
    <tr>
      <th>Store</th>
      <th>Final Price</th>
      <th>Unit Price</th>
      <th>Rating</th>
      <th>Value Score</th>
      <th>Notes</th>
    </tr>
  `;

  let lowest = Math.min(...products.map(p => p.unitPrice));

  products.forEach(p => {
    let row = table.insertRow();

    if (p.unitPrice === lowest) {
      row.classList.add("best");
    }

    row.insertCell(0).innerText = p.store;
    row.insertCell(1).innerText = "₱" + p.finalPrice.toFixed(2);
    row.insertCell(2).innerText = "₱" + p.unitPrice.toFixed(2);
    row.insertCell(3).innerText = p.rating;
    row.insertCell(4).innerText = p.valueScore.toFixed(2);
    row.insertCell(5).innerText = p.notes;
  });

  showRecommendation();
}

function showRecommendation() {
  let best = products.reduce((a, b) => a.unitPrice < b.unitPrice ? a : b);

  document.getElementById("recommendation").innerText =
    "🏆 Best Deal: " + best.store + 
    " (₱" + best.finalPrice.toFixed(2) + ", Rating: " + best.rating + ")";

  let avg = products.reduce((sum, p) => sum + p.finalPrice, 0) / products.length;
  let savings = avg - best.finalPrice;

  document.getElementById("savings").innerText =
    "💰 You saved: ₱" + savings.toFixed(2);
}
</script>

</body>
</html>
