# movie-rater
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Rate a Movie</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: system-ui, -apple-system, sans-serif;
      background: #f4f3f0;
      color: #1a1a1a;
      min-height: 100vh;
      padding: 2rem 1rem;
    }

    .wrap {
      max-width: 560px;
      margin: 0 auto;
    }

    .card {
      background: #ffffff;
      border: 1px solid #e0ddd6;
      border-radius: 16px;
      padding: 2rem;
    }

    .hero {
      text-align: center;
      margin-bottom: 2rem;
    }

    .film-icon {
      font-size: 48px;
      display: block;
      margin-bottom: 0.5rem;
    }

    h1 {
      font-size: 24px;
      font-weight: 600;
      margin-bottom: 0.25rem;
    }

    .subtitle {
      font-size: 14px;
      color: #666;
    }

    .field {
      margin-bottom: 1.25rem;
    }

    label {
      display: block;
      font-size: 13px;
      color: #555;
      margin-bottom: 6px;
    }

    input[type="text"] {
      width: 100%;
      padding: 10px 12px;
      font-size: 15px;
      border: 1px solid #ddd;
      border-radius: 8px;
      outline: none;
      background: #fff;
      color: #1a1a1a;
      transition: border-color 0.15s;
    }

    input[type="text"]:focus {
      border-color: #7f77dd;
      box-shadow: 0 0 0 3px rgba(127, 119, 221, 0.15);
    }

    .stars {
      display: flex;
      gap: 4px;
      margin-top: 4px;
    }

    .star {
      font-size: 30px;
      cursor: pointer;
      color: #ddd;
      transition: color 0.1s, transform 0.1s;
      user-select: none;
      line-height: 1;
    }

    .star.active { color: #ef9f27; }
    .star:hover { transform: scale(1.2); }

    .rating-label {
      font-size: 13px;
      color: #888;
      margin-top: 6px;
      height: 18px;
    }

    .btn {
      width: 100%;
      padding: 11px;
      font-size: 15px;
      font-weight: 600;
      border-radius: 8px;
      cursor: pointer;
      margin-top: 0.5rem;
      transition: background 0.15s, transform 0.1s;
    }

    .btn-primary {
      background: #533ab7;
      color: #fff;
      border: none;
    }

    .btn-primary:hover { background: #3c3489; }
    .btn-primary:active { transform: scale(0.98); }

    .toast {
      display: none;
      border-radius: 8px;
      padding: 10px 14px;
      font-size: 13px;
      margin-bottom: 1rem;
    }

    .reviews-section {
      display: none;
      margin-top: 2rem;
    }

    .reviews-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 1rem;
    }

    .reviews-title {
      font-size: 16px;
      font-weight: 600;
    }

    .clear-btn {
      font-size: 12px;
      color: #888;
      background: none;
      border: none;
      cursor: pointer;
    }

    .clear-btn:hover { color: #333; }

    .review-item {
      border: 1px solid #e8e5df;
      border-radius: 10px;
      padding: 12px 14px;
      margin-bottom: 10px;
      background: #f9f8f6;
    }

    .review-top {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
    }

    .review-movie { font-size: 15px; font-weight: 600; }
    .review-user { font-size: 12px; color: #888; margin-top: 2px; }

    .review-badge {
      background: #eeedfe;
      color: #3c3489;
      font-size: 13px;
      font-weight: 600;
      padding: 3px 10px;
      border-radius: 8px;
      white-space: nowrap;
    }

    .review-stars {
      font-size: 14px;
      color: #ef9f27;
      margin-top: 6px;
    }
  </style>
</head>
<body>

  <div class="wrap">
    <div class="card">
      <div class="hero">
        <span class="film-icon">🎬</span>
        <h1>Rate a Movie</h1>
        <p class="subtitle">Share your take with the world</p>
      </div>

      <div id="toast" class="toast"></div>

      <div class="field">
        <label for="uname">Your name</label>
        <input type="text" id="uname" placeholder="e.g. Alex Rivera" />
      </div>

      <div class="field">
        <label for="movie">Movie title</label>
        <input type="text" id="movie" placeholder="e.g. Inception" />
      </div>

      <div class="field">
        <label>Rating (out of 10)</label>
        <div class="stars" id="stars">
          <span class="star" data-v="1">★</span>
          <span class="star" data-v="2">★</span>
          <span class="star" data-v="3">★</span>
          <span class="star" data-v="4">★</span>
          <span class="star" data-v="5">★</span>
          <span class="star" data-v="6">★</span>
          <span class="star" data-v="7">★</span>
          <span class="star" data-v="8">★</span>
          <span class="star" data-v="9">★</span>
          <span class="star" data-v="10">★</span>
        </div>
        <div class="rating-label" id="ratingLabel">Click to rate</div>
      </div>

      <button class="btn btn-primary" onclick="submitReview()">Submit Review</button>
    </div>

    <div class="reviews-section" id="reviewsSection">
      <div class="reviews-header">
        <p class="reviews-title">Reviews</p>
        <button class="clear-btn" onclick="clearAll()">Clear all</button>
      </div>
      <div id="reviewsList"></div>
    </div>
  </div>

  <script>
    let rating = 0;
    const reviews = [];
    const labels = ['','Unwatchable','Terrible','Very bad','Bad','Mediocre','Decent','Good','Great','Excellent','Masterpiece'];
    const stars = document.querySelectorAll('.star');

    stars.forEach(s => {
      s.addEventListener('mouseenter', () => highlight(+s.dataset.v));
      s.addEventListener('mouseleave', () => highlight(rating));
      s.addEventListener('click', () => {
        rating = +s.dataset.v;
        highlight(rating);
        document.getElementById('ratingLabel').textContent = rating + '/10 — ' + labels[rating];
      });
    });

    function highlight(n) {
      stars.forEach(s => s.classList.toggle('active', +s.dataset.v <= n));
    }

    function submitReview() {
      const name = document.getElementById('uname').value.trim();
      const movie = document.getElementById('movie').value.trim();
      if (!name || !movie || !rating) {
        showToast('Please fill in all fields and select a rating.', 'error');
        return;
      }
      reviews.unshift({ name, movie, rating });
      showToast('Review submitted! Thanks, ' + name + '.', 'success');
      document.getElementById('uname').value = '';
      document.getElementById('movie').value = '';
      rating = 0;
      highlight(0);
      document.getElementById('ratingLabel').textContent = 'Click to rate';
      renderReviews();
    }

    function showToast(msg, type) {
      const t = document.getElementById('toast');
      t.textContent = msg;
      t.style.display = 'block';
      if (type === 'success') {
        t.style.background = '#eaf3de';
        t.style.color = '#3b6d11';
        t.style.border = '1px solid #97c459';
      } else {
        t.style.background = '#fcebeb';
        t.style.color = '#a32d2d';
        t.style.border = '1px solid #f09595';
      }
      setTimeout(() => t.style.display = 'none', 3000);
    }

    function renderReviews() {
      const sec = document.getElementById('reviewsSection');
      const list = document.getElementById('reviewsList');
      if (!reviews.length) { sec.style.display = 'none'; return; }
      sec.style.display = 'block';
      list.innerHTML = reviews.map(r => `
        <div class="review-item">
          <div class="review-top">
            <div>
              <div class="review-movie">${r.movie}</div>
              <div class="review-user">by ${r.name}</div>
            </div>
            <span class="review-badge">${r.rating}/10</span>
          </div>
          <div class="review-stars">${'★'.repeat(r.rating)}${'☆'.repeat(10 - r.rating)}</div>
        </div>
      `).join('');
    }

    function clearAll() {
      reviews.length = 0;
      renderReviews();
    }
  </script>

</body>
</html>
