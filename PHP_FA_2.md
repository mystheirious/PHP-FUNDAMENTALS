### PHP Fundamentals Activity

```index.php```

```php
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Order</title>
  <style>
    body {
    }
    .table-container {
      width: 30%;
      border: 2px solid black;
      padding: 1px;
    }
    table {
      width: 100%; 
      border-collapse: separate;
      border-spacing: 5px;
    }
    th, td {
      border: 1px solid black; 
      padding: 10px;
      text-align: left; 
    }
    th {
      text-align: center;
    }
  </style>
</head>
<body>
  <!-- display the menu -->
  <h1>Menu</h1>
  <div class="table-container">
    <table>
      <tr>
        <th>Order</th>
        <th>Amount</th>
      </tr>
      <tr>
        <td>Burger</td>
        <td>50</td>
      </tr>
      <tr>
        <td>Fries</td>
        <td>75</td>
      </tr>
      <tr>
        <td>Steak</td>
        <td>150</td>
      </tr>
    </table>
  </div>

  <!-- redirect to another page for the receipt -->
  <form action="checkout.php" method="GET">
    <!-- input fields for order details -->
    <p>
      <label for="order">Select an order </label>
      <select name="order" id="order">
        <option value="Burger">Burger</option>
        <option value="Fries">Fries</option>
        <option value="Steak">Steak</option>
      </select>
    </p>
    <p><label for="q">Quantity </label><input type="number" name="q" min="1" required></p>
    <p><label for="c">Cash </label><input type="number" name="c" min="0" required></p>

    <!-- submit button -->
    <p><input type="submit" value="Submit"></p>
  </form>
</body>
</html>
```

```checkout.php```

```php
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Receipt</title>
  <style>
    body {
      font-family: Arial, sans-serif;
    }
    .receipt {
      width: 50%;
      margin: 20px auto;
      border: 2px dotted black;
      padding: 20px;
    }
    .receipt h1 {
      text-align: center;
    }
    .receipt h2 {
      margin: 5px 0;
    }
    .error {
      border: 2px dotted darkred; 
      padding: 20px;
      width: 50%;
      margin: 20px auto;
      text-align: left;
    }
    .timestamp {
      font-style: italic;
      text-align: left;
    }
  </style>
</head>
<body>
  <?php
  // set the timezone
  date_default_timezone_set("Asia/Manila"); 

  // check if form is submitted
  if ($_SERVER["REQUEST_METHOD"] == "GET" && isset($_GET["order"]) && isset($_GET["q"]) && isset($_GET["c"])) {
      // Retrieve values from form
      $order = $_GET["order"];
      $quantity = $_GET["q"];
      $cash = $_GET["c"];

      // define prices for each menu item
      $prices = array(
          "Burger" => 50,
          "Fries" => 75,
          "Steak" => 150,
      );

      // calculate total cost
      $totalCost = $prices[$order] * $quantity;

      // calculate change
      $change = $cash - $totalCost;

      // check if the cash is enough for the order
      if ($cash >= $totalCost) {
          // output order summary
          $timestamp = date("m/d/Y h:i:s A");
          echo "<div class='receipt'>";
          echo "<h1>RECEIPT</h1>"; // Centered title
          echo "<h2>Total Price: $totalCost PHP</h2>";
          echo "<h2>You Paid: $cash PHP</h2>";
          echo "<h2>CHANGE: $change PHP</h2>";
          echo "<h2 class='timestamp'>$timestamp</h2>";
          echo "</div>"; // Close receipt div
      } else {
          // display an error message if balance is not enough
          echo "<div class='error'>";
          echo "<h2>Sorry, balance not enough.</h2>";
          echo "</div>";
      }
    }
  ?>
</body>
</html>
```
