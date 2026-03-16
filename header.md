```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>header</title>
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
    <style>
      * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }

      body {
        font-family: Arial, sans-serif;
        background: #fafafa;
      }

      .header {
        background: white;
        border-bottom: 1px solid #eee;
        position: sticky;
        top: 0;
        z-index: 100;
      }

      .container {
        max-width: 1200px;
        margin: auto;
        padding: 20px;
        display: flex;
        align-items: center;
        justify-content: space-between;
      }

      .logo {
        font-size: 18px;
        font-weight: 600;
      }

      .nav {
        display: flex;
        gap: 30px;
        transition:
          max-height 0.2s ease,
          opacity 0.15s ease;
      }

      .nav a {
        text-decoration: none;
        color: #333;
        font-size: 16px;
        position: relative;
        transition: color 0.2s ease;
      }

      .nav a:hover {
        color: #7a9e7e;
      }

      .menu-btn {
        display: none;
        font-size: 22px;
        cursor: pointer;
        z-index: 20;
        width: 40px;
        height: 40px;
        align-items: center;
        justify-content: center;
        border-radius: 50%;
        transition: background-color 0.2s ease;
      }

      .menu-btn:hover {
        background-color: #f0f0f0;
      }

      /*TABLET*/
      @media (min-width: 768px) and (max-width: 1024px) {
      }

      @media (max-width: 767px) {
        .container {
          padding: 10px 20px;
        }

        .menu-btn {
          display: flex;
        }

        .nav {
          position: absolute;
          top: 60px;
          left: 0;
          right: 0;
          background: white;
          flex-direction: column;
          align-items: center;
          gap: 0;
          overflow: hidden;
          max-height: 0;
          opacity: 0;
          padding: 0;
        }

        .nav a {
          display: block;
          width: 100%;
          padding: 10px 0;
          text-align: center;
          transition: background-color 0.2s ease;
        }

        .nav a:hover {
          background-color: #f0f0f0;
          color: #333;
        }

        .nav.active {
          max-height: 400px;
          opacity: 1;
          padding: 10px 0;
        }
      }
    </style>
  </head>
  <body>
    <header class="header">
      <div class="container">
        <div class="logo">🍵 TeaShop</div>
        <div class="menu-btn" id="menu-btn">
          <i class="fas fa-bars"></i>
        </div>
        <nav class="nav" id="nav">
          <a href="#">Home</a>
          <a href="#">Products</a>
          <a href="#">About</a>
          <a href="#">Contact</a>
        </nav>
      </div>
    </header>

    <div class="miau">miau</div>

    <script>
      const menuBtn = document.getElementById("menu-btn");
      const nav = document.getElementById("nav");

      menuBtn.addEventListener("click", () => {
        nav.classList.toggle("active");
      });

      // Close menu on link click
      nav.querySelectorAll("a").forEach((link) => {
        link.addEventListener("click", () => {
          nav.classList.remove("active");
        });
      });
    </script>
  </body>
</html>

```
