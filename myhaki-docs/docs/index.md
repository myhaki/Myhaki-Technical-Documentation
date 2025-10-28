# Welcome to MyHaki

<div class="carousel" id="myhaki-hero-slideshow">
  <div class="carousel-slide active">
    <img src="images/myhaki-logo.png" alt="MyHaki Logo" class="logo">
    <h2>Justice. Integrity. Accessibility.</h2>
    <p>A legal aid provision system for underserved pretrial detainees</p>
  </div>

  <div class="carousel-slide">
    <img src="images/lsk-dashboard.png" alt="LSK Dashboard" class="platform-img">
    <h2>Case Management. Pro Bono Tracking. Analytics.</h2>
    <p>Empowering LSK and lawyers for transparent justice delivery</p>
  </div>

  <div class="carousel-slide">
    <img src="images/applicant-signup.png" alt="Applicant Onboarding" class="platform-img">
    <h2>Accessible Onboarding for All</h2>
    <p>Mobile-first for low-income families and lawyers</p>
  </div>

  <div class="carousel-dots">
    <span class="carousel-dot active" data-slide="0"></span>
    <span class="carousel-dot" data-slide="1"></span>
    <span class="carousel-dot" data-slide="2"></span>
  </div>
</div>

<div class="button-container">
  <a href="https://my-haki-informational-website.vercel.app/" class="md-button">Informational Website</a>
  <a href="getting-started/" class="md-button">See what's next</a>
</div>
---

<div style="text-align: center; font-size: 1.18rem; color: #683929; margin: 2em 0;">
  <strong>MyHaki</strong> is an AI-driven case management system,<br>
  connecting lawyers with underserved pretrial detainees, streamlining access to legal aid and<br> enabling the Law Society of Kenya to monitor case progress and incentivize lawyers through rewards thereby enhancing access to justice.
 </div>
---

## Why MyHaki?

<div class="myhaki-card">
  <h3>Agentic AI Case Filtration</h3>
  <p>Classifies, and prioritizes cases (civil/criminal, urgency) for fair assignment.</p>
</div>

<div class="myhaki-card">
  <h3>Geolocation Matching</h3>
  <p>Assigns lawyers based on proximity using LocationIQ and the Haversine formula.</p>
</div>

<div class="myhaki-card">
  <h3>Secure Android App for Lawyers & Applicants</h3>
  <p>Mobile-first onboarding, case tracking, CPD points tracking, and case dashboards.</p>
</div>

<div class="myhaki-card">
  <h3>LSK Oversight Dashboard</h3>
  <p>Next.js-powered web interface for admin, compliance, analytics, and reporting.</p>
</div>

<div class="myhaki-card">
  <h3>Compliance & Data Protection</h3>
  <p>Built on Legal Aid Act 2016 and Kenya Data Protection Act, with encrypted storage and audit trails.</p>
</div>

<div class="myhaki-card">
  <h3>API-First Architecture</h3>
  <p>All features accessible via robust REST APIs (<a href="api-reference/">see API Reference</a>).</p>
</div>

---

## Platform Highlights

<div style="text-align: center; margin: 2em 0;">
  <img src="images/deployment-diagram.png" alt="MyHaki Platform Overview" class="platform-img">
</div>

---

<div class="mission-banner">
  <strong>Our Mission:</strong> <em>Enhance access to justice for Kenya’s most vulnerable.<br>
  Enable fast, fair, and transparent legal aid service delivery powered by technology.</em>
</div>

---

> _Learn more about MyHaki on our [Website](https://my-haki-informational-website.vercel.app/)._  
> _Ready to get started? [Start here](getting-started.md)!_

<script>
document.addEventListener('DOMContentLoaded', function() {
  const slides = document.querySelectorAll('.carousel-slide');
  const dots = document.querySelectorAll('.carousel-dot');
  let current = 0;

  function showSlide(index) {
    slides.forEach((slide, i) => {
      slide.classList.toggle('active', i === index);
    });
    dots.forEach((dot, i) => {
      dot.classList.toggle('active', i === index);
    });
    current = index;
  }

  dots.forEach((dot, i) => {
    dot.addEventListener('click', () => showSlide(i));
  });

  setInterval(() => {
    showSlide((current + 1) % slides.length);
  }, 6000);

  showSlide(0);
});
</script>