```html
<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">

<!-- Makes the layout responsive on mobile devices -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Simple Carousel</title>

<!-- FontAwesome library for the arrow icons -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>

/* ============================= */
/* PAGE STYLING */
/* ============================= */

body {
  font-family: Arial, sans-serif;

  /* Full height of the screen */
  height: 100vh;

  /* Flexbox used to center the carousel horizontally and vertically */
  display: flex;
  justify-content: center;
  align-items: center;

  /* Light gray background */
  background: #f0f0f0;
}

/* Container that holds arrows and carousel */
.wrapper {
  display: flex;          /* Put elements in a row */
  align-items: center;    /* Vertically align them */
  gap: 15px;              /* Space between arrows and carousel */
}

/* ============================= */
/* CAROUSEL STRUCTURE */
/* ============================= */

/* Visible window of the carousel */
.carousel {
  width: 600px;       /* Exactly 3 slides (200px each) */
  overflow: hidden;   /* Hide slides that go outside the area */
}

/* Track that holds all slides */
.track {
  display: flex;      /* Slides sit next to each other horizontally */

  /* Smooth animation when slides move left/right */
  transition: transform 0.4s ease;
}

/* ============================= */
/* INDIVIDUAL SLIDES */
/* ============================= */

.slide {
  min-width: 200px;   /* Each slide is 200px wide */
  height: 200px;      /* Square shape */

  padding: 8px;       /* Small spacing around image */
  box-sizing: border-box;

  /* Make non-active slides slightly faded */
  opacity: 0.5;

  /* Make them slightly smaller */
  transform: scale(0.9);

  /* Smooth animation when active slide changes */
  transition: all 0.3s;
}

/* Active (center) slide */
.slide.active {
  opacity: 1;        /* Fully visible */
  transform: scale(1); /* Slightly bigger */
}

/* Image inside the slide */
.slide img {
  width: 100%;       /* Fill the slide width */
  height: 100%;      /* Fill the slide height */

  /* Keeps image proportions while filling the square */
  object-fit: cover;

  border-radius: 10px; /* Rounded corners */
}

/* ============================= */
/* ARROW BUTTONS */
/* ============================= */

.arrow {
  font-size: 20px;

  border: none;
  background: white;

  width: 45px;
  height: 45px;

  border-radius: 50%;  /* Make button circular */

  cursor: pointer;

  /* Small shadow to make the button pop */
  box-shadow: 0 2px 6px rgba(0,0,0,0.15);
}

/* Hover effect for arrows */
.arrow:hover {
  background: #eaeaea;
}

</style>
</head>

<body>

<!-- Main container -->
<div class="wrapper">

  <!-- Left arrow button -->
  <button class="arrow left">
    <i class="fa-solid fa-chevron-left"></i>
  </button>

  <!-- Carousel viewing window -->
  <div class="carousel">

    <!-- Track that slides left/right -->
    <div class="track">

      <!-- Individual slides -->
      <div class="slide"><img src="https://picsum.photos/400?random=1"></div>
      <div class="slide"><img src="https://picsum.photos/400?random=2"></div>
      <div class="slide"><img src="https://picsum.photos/400?random=3"></div>
      <div class="slide"><img src="https://picsum.photos/400?random=4"></div>
      <div class="slide"><img src="https://picsum.photos/400?random=5"></div>

    </div>
  </div>

  <!-- Right arrow button -->
  <button class="arrow right">
    <i class="fa-solid fa-chevron-right"></i>
  </button>

</div>


<script>

/* ============================= */
/* SELECTING ELEMENTS */
/* ============================= */

/* Select the track that moves */
const track = document.querySelector(".track");

/* Convert slide elements into an array */
const slides = Array.from(track.children);

/* Select arrow buttons */
const prevBtn = document.querySelector(".left");
const nextBtn = document.querySelector(".right");

/* ============================= */
/* SETTINGS */
/* ============================= */

/* Width of each slide (must match CSS) */
const SLIDE_WIDTH = 200;

/* Start on slide index 1 so the second slide appears centered */
let currentIndex = 1;


/* ============================= */
/* MAIN FUNCTION */
/* ============================= */

function updateCarousel() {

  /* Remove active class from all slides */
  slides.forEach(s => s.classList.remove("active"));

  /* Add active class to the current slide */
  slides[currentIndex].classList.add("active");

  /*
  Calculate how far the track should move.

  Example:
  if currentIndex = 2
  (2 - 1) * -200 = -200px

  This shifts the slides so the chosen one appears in the center.
  */
  const move = (currentIndex - 1) * -SLIDE_WIDTH;

  /* Move the entire track horizontally */
  track.style.transform = `translateX(${move}px)`;

  /* Disable arrows when reaching the first or last slide */
  prevBtn.disabled = currentIndex === 0;
  nextBtn.disabled = currentIndex === slides.length - 1;
}


/* ============================= */
/* BUTTON EVENTS */
/* ============================= */

/* When clicking the right arrow */
nextBtn.addEventListener("click", () => {

  /* Move forward if not at last slide */
  if (currentIndex < slides.length - 1) currentIndex++;

  updateCarousel();
});


/* When clicking the left arrow */
prevBtn.addEventListener("click", () => {

  /* Move backward if not at first slide */
  if (currentIndex > 0) currentIndex--;

  updateCarousel();
});


/* Run the function once when page loads */
updateCarousel();

</script>

</body>
</html>
```
