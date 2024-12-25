<html lang="en">
<head>

  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Title with Image</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
    }
    .page-header {
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      padding: 50px 20px;
      background-image: url('back3.jpeg'); 
      background-size: 100%; 
      background-position: center;
      background-repeat: no-repeat;
      text-align: center; 
    }
    .page-header h1, .page-header h2, .page-header h3 {
      margin: 0;
      color: black;
    }
    .page-header h1 {
      font-size: 32px;
    }
    .page-header h2 {
      margin: 5px 0;
      font-size: 20px;
      color: black;
    }
    .page-header h3 {
      margin: 5px 0;
      font-size: 16px;
      color: black; 
    }  
    .btn {
      text-decoration: none;
      color: black;
      background-color: rgba(0, 123, 255, 0.5); 
      padding: 8px 12px;
      border-radius: 4px;
      margin: 5px;
      font-size: 14px;
    }
    .btn:hover {
      background-color: rgba(0, 86, 179, 0.5);
    }
    #sidebar {
      width: 220px;
      position: fixed;
      left: -250px;
      top: 0;
      bottom: 0;
      background-color: #f4f4f4;
      overflow-y: auto;
      padding: 10px;
      box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
      transition: left 0.3s ease-in-out; 
    }
    #sidebar.active {
      left: 0; 
    }
    #sidebar h2 {
      font-size: 18px;
      margin-bottom: 10px;
    }
    #sidebar ul {
      list-style: none;
      padding: 0;
    }
      #sidebar ul ul {
        padding-left: 20px; 
      }
      #sidebar ul ul li a {
        font-size: 0.9em; 
        color: #666;
    }
    #toggle-btn {
      position: fixed;
      top: 10px;
      left: 10px;
      background-color: #333;
      color: #fff;
      border: none;
      padding: 10px 15px;
      cursor: pointer;
      z-index: 1000;
    }
  </style>
</head>
<body>

  <header class="page-header" role="banner">
    <h1 class="project-name">Driven to Discover: A Data-Driven Analysis and Prediction of Taxi Trip Durations</h1>
    <h2 class="project-tagline">Drake Graham7</h2>
    <h3 class="project-tagline">dgraham7362@gmail.com</h3>
    <a href="https://github.com/dgraham6/Taxi-EDA" class="btn" style="background-color: #8ec27c; color: black;">View on GitHub</a>
    <a href="https://www.linkedin.com/in/drake-graham-a82048240/" class="btn" style="background-color: #8ec27c; color: black;">LinkedIn</a>
  </header>

</body>
</html>
clusion
