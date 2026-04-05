// ==================== Users Database ====================
let users = JSON.parse(localStorage.getItem("smartShippingUsers")) || [
  {
    username: "admin",
    email: "admin@smartshipping.sy",
    password: "admin123",
    name: "المدير"
  },
  {
    username: "user",
    email: "user@smartshipping.sy",
    password: "user123",
    name: "مستخدم"
  }
];

let currentUser = JSON.parse(localStorage.getItem("currentUser")) || null;

function saveUsers() {
  localStorage.setItem("smartShippingUsers", JSON.stringify(users));
}

// ==================== Preloader ====================
window.addEventListener("load", () => {
  setTimeout(() => {
    document.getElementById("preloader").classList.add("hidden");
  }, 2500);

  if (currentUser) {
    updateUIForLoggedInUser();
  }

  initAnimations();
});

// ==================== Header Scroll Effect ====================
window.addEventListener("scroll", () => {
  const header = document.getElementById("header");
  const backToTop = document.getElementById("backToTop");

  if (window.scrollY > 100) {
    header.classList.add("scrolled");
  } else {
    header.classList.remove("scrolled");
  }

  if (window.scrollY > 500) {
    backToTop.classList.add("visible");
  } else {
    backToTop.classList.remove("visible");
  }

  updateActiveNavLink();
});

// ==================== Update Active Nav Link ====================
function updateActiveNavLink() {
  const sections = document.querySelectorAll("section[id]");
  const navLinks = document.querySelectorAll(".nav-menu a");

  let currentSection = "";

  sections.forEach((section) => {
    const sectionTop = section.offsetTop - 150;
    if (window.scrollY >= sectionTop) {
      currentSection = section.getAttribute("id");
    }
  });

  navLinks.forEach((link) => {
    link.classList.remove("active");
    if (link.getAttribute("href") === `#${currentSection}`) {
      link.classList.add("active");
    }
  });
}

// ==================== Hero Slider ====================
let currentSlide = 0;
const slides = document.querySelectorAll(".slide");
const dots = document.querySelectorAll(".slider-dot");
let slideInterval;

function goToSlide(index) {
  slides[currentSlide].classList.remove("active");
  dots[currentSlide].classList.remove("active");
  currentSlide = index;
  slides[currentSlide].classList.add("active");
  dots[currentSlide].classList.add("active");
}

function nextSlide() {
  goToSlide((currentSlide + 1) % slides.length);
}

function prevSlide() {
  goToSlide((currentSlide - 1 + slides.length) % slides.length);
}

function startSlider() {
  slideInterval = setInterval(nextSlide, 5000);
}

function stopSlider() {
  clearInterval(slideInterval);
}

startSlider();

const heroSlider = document.querySelector(".hero-slider");
if (heroSlider) {
  heroSlider.addEventListener("mouseenter", stopSlider);
  heroSlider.addEventListener("mouseleave", startSlider);
}

// ==================== Mobile Menu ====================
function toggleMenu() {
  const navMenu = document.getElementById("navMenu");
  navMenu.classList.toggle("active");
  document.body.style.overflow = navMenu.classList.contains("active")
    ? "hidden"
    : "auto";
}

document.querySelectorAll(".nav-menu a").forEach((link) => {
  link.addEventListener("click", () => {
    document.getElementById("navMenu").classList.remove("active");
    document.body.style.overflow = "auto";
  });
});

// ==================== Modal Functions ====================
function openModal(modalId) {
  const modal = document.getElementById(modalId);
  modal.classList.add("active");
  document.body.style.overflow = "hidden";
}

function closeModal(modalId) {
  const modal = document.getElementById(modalId);
  modal.classList.remove("active");
  document.body.style.overflow = "auto";

  document.querySelectorAll(".error-message, .success-message").forEach((m) => {
    m.style.display = "none";
  });
}

document.querySelectorAll(".modal").forEach((modal) => {
  modal.addEventListener("click", function (e) {
    if (e.target === this) {
      this.classList.remove("active");
      document.body.style.overflow = "auto";
    }
  });
});

document.addEventListener("keydown", (e) => {
  if (e.key === "Escape") {
    document.querySelectorAll(".modal.active").forEach((modal) => {
      modal.classList.remove("active");
      document.body.style.overflow = "auto";
    });
  }
});

// ==================== Tab Switching ====================
function switchTab(tab) {
  const tabs = document.querySelectorAll(".modal-tab");
  const contents = document.querySelectorAll(".tab-content");

  tabs.forEach((t) => t.classList.remove("active"));
  contents.forEach((c) => c.classList.remove("active"));

  if (tab === "login") {
    tabs[0].classList.add("active");
    document.getElementById("loginTab").classList.add("active");
  } else {
    tabs[1].classList.add("active");
    document.getElementById("registerTab").classList.add("active");
  }

  document.querySelectorAll(".error-message, .success-message").forEach((m) => {
    m.style.display = "none";
  });
}

// ==================== Login Handler ====================
function handleLogin(e) {
  e.preventDefault();

  const username = document.getElementById("loginUsername").value.trim();
  const password = document.getElementById("loginPassword").value;
  const errorEl = document.getElementById("loginError");
  const successEl = document.getElementById("loginSuccess");

  errorEl.style.display = "none";
  successEl.style.display = "none";

  const user = users.find(
    (u) =>
      (u.username.toLowerCase() === username.toLowerCase() ||
        u.email.toLowerCase() === username.toLowerCase()) &&
      u.password === password
  );

  if (user) {
    successEl.style.display = "block";

    currentUser = user;
    localStorage.setItem("currentUser", JSON.stringify(user));

    setTimeout(() => {
      closeModal("authModal");
      updateUIForLoggedInUser();
      openModal("dashboardModal");
      document.getElementById("loginForm").reset();
    }, 1500);
  } else {
    errorEl.style.display = "block";
  }
}

// ==================== Register Handler ====================
function handleRegister(e) {
  e.preventDefault();

  const name = document.getElementById("regName").value.trim();
  const email = document.getElementById("regEmail").value.trim();
  const username = document.getElementById("regUsername").value.trim();
  const password = document.getElementById("regPassword").value;
  const confirmPassword = document.getElementById("regConfirmPassword").value;
  const errorEl = document.getElementById("registerError");
  const successEl = document.getElementById("registerSuccess");

  errorEl.style.display = "none";
  successEl.style.display = "none";

  if (password.length < 6) {
    errorEl.textContent = "كلمة المرور يجب أن تكون 6 أحرف على الأقل";
    errorEl.style.display = "block";
    return;
  }

  if (password !== confirmPassword) {
    errorEl.textContent = "كلمات المرور غير متطابقة";
    errorEl.style.display = "block";
    return;
  }

  if (users.find((u) => u.username.toLowerCase() === username.toLowerCase())) {
    errorEl.textContent = "اسم المستخدم موجود مسبقاً";
    errorEl.style.display = "block";
    return;
  }

  if (users.find((u) => u.email.toLowerCase() === email.toLowerCase())) {
    errorEl.textContent = "البريد الإلكتروني مستخدم مسبقاً";
    errorEl.style.display = "block";
    return;
  }

  const newUser = { name, email, username, password };
  users.push(newUser);
  saveUsers();

  successEl.style.display = "block";

  setTimeout(() => {
    switchTab("login");
    document.getElementById("loginUsername").value = username;
    document.getElementById("registerForm").reset();
  }, 1500);
}

// ==================== Update UI for Logged In User ====================
function updateUIForLoggedInUser() {
  const authBtn = document.getElementById("authBtn");
  authBtn.innerHTML = `<i class="fas fa-user-check"></i>`;
  authBtn.onclick = () => openModal("dashboardModal");
  document.getElementById("welcomeName").textContent = currentUser.name;
}

// ==================== Logout ====================
function logout() {
  currentUser = null;
  localStorage.removeItem("currentUser");
  closeModal("dashboardModal");

  const authBtn = document.getElementById("authBtn");
  authBtn.innerHTML = '<i class="fas fa-user"></i>';
  authBtn.onclick = () => openModal("authModal");
}

// ==================== FAQ Toggle ====================
function toggleFaq(element) {
  const item = element.parentElement;
  const wasActive = item.classList.contains("active");

  document.querySelectorAll(".faq-item").forEach((i) => {
    i.classList.remove("active");
  });

  if (!wasActive) {
    item.classList.add("active");
  }
}

// ==================== Counter Animation ====================
function animateCounters() {
  const counters = document.querySelectorAll(".counter");

  counters.forEach((counter) => {
    const target = parseInt(counter.getAttribute("data-target"));
    const duration = 2000;
    const step = target / (duration / 16);
    let current = 0;

    const updateCounter = () => {
      current += step;
      if (current < target) {
        counter.textContent = Math.floor(current).toLocaleString("ar-SA");
        requestAnimationFrame(updateCounter);
      } else {
        counter.textContent = target.toLocaleString("ar-SA");
      }
    };

    updateCounter();
  });
}

// ==================== Intersection Observer for Counters ====================
const statsSection = document.querySelector(".stats-section");
let counterAnimated = false;

if (statsSection) {
  const counterObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting && !counterAnimated) {
          animateCounters();
          counterAnimated = true;
        }
      });
    },
    { threshold: 0.5 }
  );

  counterObserver.observe(statsSection);
}

// ==================== Testimonials Slider ====================
let testimonialIndex = 0;
const testimonialsTrack = document.getElementById("testimonialsTrack");
const testimonials = document.querySelectorAll(".testimonial-card");
const tDots = document.querySelectorAll(".t-dot");

function goToTestimonial(index) {
  testimonialIndex = index;
  if (testimonialsTrack) {
    testimonialsTrack.style.transform = `translateX(${
      testimonialIndex * 100
    }%)`;
  }

  tDots.forEach((dot, i) => {
    dot.classList.toggle("active", i === index);
  });
}

if (testimonials.length > 0) {
  setInterval(() => {
    testimonialIndex = (testimonialIndex + 1) % testimonials.length;
    goToTestimonial(testimonialIndex);
  }, 5000);
}

// ==================== Gallery Click ====================
document.querySelectorAll(".gallery-item").forEach((item) => {
  item.addEventListener("click", () => {
    const img = item.querySelector("img");
    document.getElementById("modalImage").src = img.src;
    openModal("imageModal");
  });
});

// ==================== Contact Form ====================
const contactForm = document.getElementById("contactForm");
if (contactForm) {
  contactForm.addEventListener("submit", function (e) {
    e.preventDefault();
    alert("شكراً لتواصلك معنا! سنرد عليك قريباً.");
    this.reset();
  });
}

// ==================== Smooth Scroll ====================
document.querySelectorAll('a[href^="#"]').forEach((anchor) => {
  anchor.addEventListener("click", function (e) {
    e.preventDefault();
    const target = document.querySelector(this.getAttribute("href"));
    if (target) {
      const headerHeight = document.querySelector(".header").offsetHeight;
      const targetPosition = target.offsetTop - headerHeight;

      window.scrollTo({
        top: targetPosition,
        behavior: "smooth"
      });
    }
  });
});

// ==================== Back to Top ====================
function scrollToTop() {
  window.scrollTo({
    top: 0,
    behavior: "smooth"
  });
}

// ==================== Animation on Scroll ====================
function initAnimations() {
  const animateElements = document.querySelectorAll(
    ".service-card, .gallery-item, .stat-item, .contact-info-card, .location-card, .feature-item, .about-feature, .why-us-item, .container-card, .warehouse-card, .coverage-area"
  );

  const animateObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry, index) => {
        if (entry.isIntersecting) {
          setTimeout(() => {
            entry.target.style.opacity = "1";
            entry.target.style.transform = "translateY(0)";
          }, index * 50);
        }
      });
    },
    { threshold: 0.1 }
  );

  animateElements.forEach((el) => {
    el.style.opacity = "0";
    el.style.transform = "translateY(30px)";
    el.style.transition = "all 0.6s ease";
    animateObserver.observe(el);
  });
}

// ==================== Search Functionality ====================
const searchInput = document.querySelector(".search-input");
if (searchInput) {
  searchInput.addEventListener("keypress", function (e) {
    if (e.key === "Enter") {
      const searchTerm = this.value.trim();
      if (searchTerm) {
        alert(`جاري البحث عن: ${searchTerm}`);
      }
    }
  });
}

// ==================== Console Welcome ====================
console.log(
  "%c Smart Shipping Services ",
  "background: linear-gradient(135deg, #D4AF37, #1E90FF); color: #fff; font-size: 20px; padding: 10px 20px; border-radius: 10px; font-weight: bold;"
);
console.log(
  "%c مرحباً بك في موقع خدمة الشحن الذكية! ",
  "color: #D4AF37; font-size: 14px;"
);
