+++
date = '2026-07-04T16:09:40+02:00'
title = 'Home'
+++

<style>

.site-title {
    display: none;
}

figure {
    margin: 0;
}
.hero {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 2rem;
}

.hero > div:first-child {
  flex: 1;
  min-width: 300px;
}

.hero > div:last-child {
  flex: 0 0 auto;
}

@media (max-width: 45rem) {
  .hero {
    flex-direction: column;
  }

  .hero > div:last-child {
    margin-bottom: 2rem;
  }

  h1 {
    margin: 0 auto;
  }
}
</style>

<section class="hero">
<div>
<h1 style="font-size: 4rem; line-height: 1.1">
    <span style="color: #7a0000">Matyáš&nbsp;Jan</span><br>
    Kudláček
</h1>

</div>
<div>

<figure>
    <picture>
      <img src="/plenarni4.jpg" alt="Matyáš Jan Kudláček" loading="eager" width="300" height="420" style="border-radius: 1rem;">
    </picture>
</figure>
</div>
</section>
