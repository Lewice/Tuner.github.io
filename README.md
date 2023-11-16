<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Menu Calculator and Form Submission</title>
  <script src="https://code.jquery.com/jquery-3.4.1.js" integrity="sha256-WpOohJOqMqqyKL9FccASB9O0KwACQJpFTUBLTYOVvVU=" crossorigin="anonymous"></script>
  <style>
    body, h2, form, label, p, button, select, input {
      font-size: 8;
      margin-right: 10px; /* Add a margin for spacing */
    }

    label {
		display: block;
		margin-bottom: 5px;
	}

    body, h2, form {
      text-align: center;
    }

    p {
      text-align: center;
      margin: 0; /* Remove default margin for <p> */
    }

    button {
      margin-top: 10px;
    }
	body, h2 {
	font-weight: bold;
	}
	body {
	background-color: grey;
	}
  </style>
  <script>
    function calculateTotals() {
  let total = 0;

  // Calculate total from selected items
  const menuItems = document.querySelectorAll('.menu-item:checked');
  menuItems.forEach(item => {
    const price = parseFloat(item.dataset.price);
    const quantity = parseInt(item.nextElementSibling.value);

    if (!isNaN(price) && !isNaN(quantity) && quantity > 0) {
      // Exclude "Mystery Box" from discounts
      if (item.classList.contains('exclude-discount')) {
        total += price * quantity;
      } else {
        total += price * quantity * (1 - ($("#discount").val() / 100));
      }
    }
  });

  // Change commission rate from 5% to 10%
  const commission = total * 0.10;

  document.getElementById('total').innerText = total.toFixed(2);
  document.getElementById('commission').innerText = commission.toFixed(2);
}

    function SubForm() {
  // Check if the employee name is provided
  const employeeName = $("#employeeName").val();
  if (employeeName.trim() === "") {
    alert("Employee Name is required!");
    return;
  }

  // Get selected menu items and quantities
  const orderedItems = [];
  const menuItems = document.querySelectorAll('.menu-item:checked');
  menuItems.forEach(item => {
    const itemName = item.parentNode.textContent.trim();
    const price = parseFloat(item.dataset.price);
    const quantity = parseInt(item.nextElementSibling.value);

    if (!isNaN(price) && !isNaN(quantity) && quantity > 0) {
      orderedItems.push({
        name: itemName,
        price: price,
        quantity: quantity
      });
    }
  });

  // Calculate total and commission
  const total = parseFloat($("#total").text());
  const commission = parseFloat($("#commission").text());
  const discount = parseFloat($("#discount").val());

  // Prepare data for Discord webhook
  const discordData = {
  username: "Menu Order Bot",
  content: `New order submitted by ${employeeName}`,
  embeds: [{
    title: "Order Details",
    fields: [
      { name: "Employee Name", value: employeeName, inline: true },
      { name: "Total", value: `$${total.toFixed(2)}`, inline: true },
      { name: "Commission", value: `$${commission.toFixed(2)}`, inline: true },
      { name: "Discount Applied", value: `${discount}%`, inline: true },
      { name: "Items Ordered", value: orderedItems.map(item => `${item.quantity}x ${item.name}`).join('\n') }
    ],
    color: 0x00ff00 // You can customize the color
  }]
};

  // Form Submission Logic for Spreadsheet
  $.ajax({
    url: "https://api.apispreadsheets.com/data/LC93Ampj4hkWMt1K/",
    type: "post",
    data: {
      "Employee Name": employeeName,
      "Total": total.toFixed(2),
      "Commission": commission.toFixed(2),
      "Items Ordered": JSON.stringify(orderedItems),
      "Discount Applied": discount
    },
    success: function () {
      alert("Form Data Submitted to Spreadsheet and Discord :)");
      // Reset the form after submission
      resetForm();
    },
    error: function () {
      alert("There was an error :(");
    }
  });

  // Form Submission Logic for Discord webhook
  $.ajax({
    url: "https://discordapp.com/api/webhooks/1153842904373145681/aToukg4NWWCVmM8ygHgRnuqxmEag4GOIA8lCw6IE4hjkyg5VofbuYnIRolvUIPTJYHet",
    type: "post",
    contentType: "application/json",
    data: JSON.stringify(discordData),
    success: function () {
      // Do nothing specific for Discord success
    },
    error: function () {
      console.error("Error sending data to Discord :(");
    }
  });
}

    function resetForm() {
      // Reset checkboxes and quantity inputs
      $('.menu-item').prop('checked', false);
      $('.quantity').val(1);

      // Reset totals
      document.getElementById('total').innerText = '';
      document.getElementById('commission').innerText = '';
      // Reset discount dropdown to default
      $("#discount").val("0");
    }
  </script>
</head>
<body>

  <h2>Paleto Tuners</h2>

  <form id="menuForm">
  <h3>Engine Upgrades</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000"> Engine Tier 1 - $1000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="3000"> Engine Tier 2 - $3000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="7000"> Engine Tier 3 - $7000
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Suspension Upgrades</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000"> Suspension Tier 1 - $1000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="3000"> Suspension Tier 2 - $3000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="5000"> Suspension Tier 3 - $5000
      <input type="number" class="quantity" value="1" min="1">
    </label>

	
	<h3>Transmission Upgrades</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000"> Transmission Tier 1- $1000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="4000"> Transmission Tier 2 - $4000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="7000"> Transmission Tier 3 - $7000
      <input type="number" class="quantity" value="1" min="1">
    </label>

	
	<h3>Brake Upgrades</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000"> Brakes Tier 1 - $1000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="5000"> Brakes Tier 2 - $5000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="7000"> Brakes Tier 3 - $7000
      <input type="number" class="quantity" value="1" min="1">
    </label>

	
	<h3>Turbo</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="12000"> Turbo - $12,000
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Repairs</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1200"> Standard Repair (D-A Class) - $1200
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="1600"> S-Class Repair - $1600
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Misc Items</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="300"> Single Lockpick - $300 
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="1500"> Adavanced Lockpick - $1500
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="350"> Basic Repair Kit - $350
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000"> Advanced Repair Kit - $1000
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="400"> Cleaning Kit - $400
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000"> Car Polish(1-2 days) - $1000
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="2000"> Fantastic Wax (3-4 days) - $2000
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="25000"> Harness - 25,000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Upgrade Removal</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="2000"> Upgrade Removal - 2000$ 
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Camber</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="7000"> Camber - $7000 
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Nos</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="2000"> Nos Color Changer - 2000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="12000"> Nos - 12,000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Towing</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="750"> Los Santos - 750$
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="500"> Sandy - 500$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="250"> Paleto - 250$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Discounted Repairs</h3>
    <label>
      <input type="checkbox" class="menu-item exclude-discount" data-price="1000"> EMS, LEO, DOC, DOJ - 1000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item exclude-discount" data-price="1000"> A-D Class BlackWood Saloons - 1,000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item exclude-discount" data-price="1000"> A-D Class Garcon Pawn - 1,000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Discounted Items</h3>
    <label>
      <input type="checkbox" class="menu-item exclude-discount" data-price="20000"> Harness (Employee) - 20,000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item exclude-discount" data-price="20000"> Harness (pd) - 20,000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item exclude-discount" data-price="10000"> NOS (Employee) - 10,000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	<h3>Discounted Items</h3>
    <label>
      <input type="checkbox" class="menu-item exclude-discount" data-price="8000"> Long Range Radio's - 8000$
      <input type="number" class="quantity" value="1" min="1">
    </label>
    	
	
	
	
	
	<div style="margin-bottom: 30px;"></div>
	
	<label for="discount">Select Discount:</label>
    <select id="discount" onchange="calculateTotals()">
      <option value="0">No Discount</option>
      <option value="50">50% Discount (Employee Discount)</option>
    </select>
	
	<div style="margin-bottom: 30px;"></div>
	


    <label for="employeeName">Employee Name:</label>
    <input type="text" id="employeeName" required>
	
	<div style="margin-bottom: 30px;"></div>
	
	

    <p>Total: $<span id="total"></span></p>
    <p>Commission (10%): $<span id="commission"></span></p>
	
	<div style="margin-bottom: 30px;"></div>

    <button type="button" onclick="calculateTotals()">Calculate</button>
    <button type="button" onclick="SubForm()">Submit</button>
    <button type="button" onclick="resetForm()">Reset</button>
  </form>

</body>
</html>
