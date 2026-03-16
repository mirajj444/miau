```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Simple Carousel</title>

    <!-- Font Awesome for arrow icons -->
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css" />

    <style>
      /* Center everything on the page */
      body {
        font-family: Arial, sans-serif;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        background: #f0f0f0;
      }

      /* Wrapper holds arrows + carousel */
      .wrapper {
        display: flex;
        align-items: center;
        gap: 15px; /* space between arrows and images */
      }

      /* Main carousel container */
      .carousel {
        width: 300px;
        height: 300px; /* square shape */
        overflow: hidden; /* hides slides outside */
      }

      /* Holds all slides in one row */
      .track {
        display: flex;
        transition: transform 0.5s ease; /* smooth sliding */
      }

      /* Each slide */
      .slide {
        min-width: 100%;
        opacity: 0.5; /* inactive slides faded */
        transform: scale(0.85); /* slightly smaller */
        transition: all 0.4s ease;
      }

      /* Active slide (center image) */
      .slide.active {
        opacity: 1;
        transform: scale(1); /* full size */
      }

      /* Make images square */
      .slide img {
        width: 100%;
        height: 300px;
        object-fit: cover; /* keeps image nicely cropped */
        border-radius: 10px;
      }

      /* Arrow buttons */
      .arrow {
        font-size: 20px;
        border: none;
        background: white;
        width: 45px;
        height: 45px;
        border-radius: 50%;
        cursor: pointer;
      }

      .arrow:hover {
        background: #eaeaea;
      }
    </style>
  </head>

  <body>
    <!-- WRAPPER -->
    <!-- contains arrows and carousel -->
    <div class="wrapper">
      <!-- LEFT ARROW -->
      <button class="arrow left">
        <i class="fa-solid fa-chevron-left"></i>
      </button>

      <!-- CAROUSEL -->
      <div class="carousel">
        <!-- Track that slides left and right -->
        <div class="track">
          <!-- Slide 1 (starts active) -->
          <div class="slide active">
            <img src="https://picsum.photos/400?random=1" />
          </div>

          <!-- Slide 2 -->
          <div class="slide">
            <img src="https://picsum.photos/400?random=2" />
          </div>

          <!-- Slide 3 -->
          <div class="slide">
            <img src="https://picsum.photos/400?random=3" />
          </div>

          <div class="slide">
            <img src="https://picsum.photos/400?random=4" />
          </div>
        </div>
      </div>

      <!-- RIGHT ARROW -->
      <button class="arrow right">
        <i class="fa-solid fa-chevron-right"></i>
      </button>
    </div>

    <script>
      /* Select important elements from the page */
      const track = document.querySelector(".track");
      const slides = Array.from(track.children);
      const prevBtn = document.querySelector(".left");
      const nextBtn = document.querySelector(".right");

      /* Keeps track of current slide */
      let currentIndex = 0;

      /* Function that updates which slide is visible */
      function updateCarousel() {
        /* Remove active class from every slide */
        slides.forEach((slide) => {
          slide.classList.remove("active");
        });

        /* Add active class to the current slide */
        slides[currentIndex].classList.add("active");

        /* Move the slide container left/right */
        const move = -currentIndex * 100;
        track.style.transform = "translateX(" + move + "%)";
      }

      /* When clicking NEXT arrow */
      nextBtn.addEventListener("click", () => {
        currentIndex++;

        /* If at last slide, loop back to start */
        if (currentIndex >= slides.length) {
          currentIndex = 0;
        }

        updateCarousel();
      });

      /* When clicking PREVIOUS arrow */
      prevBtn.addEventListener("click", () => {
        currentIndex--;

        /* If before first slide, go to last */
        if (currentIndex < 0) {
          currentIndex = slides.length - 1;
        }

        updateCarousel();
      });
    </script>
  </body>
</html>

```
