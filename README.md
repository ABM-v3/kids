# LittlePlan 🎨

**Free weekly activity planner for kids aged 2–12.**

> Works 100% without AI or a backend. Pure HTML/CSS/JS — deploy anywhere for free.

---

## What it does

- Parent fills in child's age, interests, time available
- Generates a 7-day activity plan from a library of 80+ hand-picked activities
- Each activity shows name, description, materials needed, and duration
- Plans are printable as PDF from the browser
- Shareable via a unique URL (plan is encoded in the URL, no database needed)
- Free tier: 5 plans per day per browser (tracked in localStorage)

---

## Project structure

```
littleplan/
├── public/
│   ├── index.html      ← full page (hero + form + results + FAQ)
│   ├── styles.css      ← all styling, responsive, print-ready
│   ├── activities.js   ← activity library (80+ activities, no AI)
│   └── app.js          ← all logic (plan generation, rendering, share)
├── vercel.json         ← Vercel deploy config
├── netlify.toml        ← Netlify deploy config
└── .github/
    └── workflows/
        └── deploy.yml  ← GitHub Pages auto-deploy
```

---

## Deploy in 2 minutes (pick one)

### Option A — Vercel (recommended, fastest)

1. Go to [vercel.com](https://vercel.com) and sign up free
2. Click **"Add New Project"** → **"Import Git Repository"**
3. Connect your GitHub account and select this repo
4. Vercel auto-detects the config — click **Deploy**
5. Live at `https://your-project.vercel.app` in ~30 seconds

Or use the CLI:
```bash
npm i -g vercel
cd littleplan
vercel
```

---

### Option B — Netlify

1. Go to [netlify.com](https://netlify.com) and sign up free
2. Click **"Add new site"** → **"Import an existing project"**
3. Connect GitHub and select this repo
4. Set **Publish directory** to `public`
5. Click **Deploy site**

Or drag-and-drop:
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag the `public/` folder into the browser
3. Done — live instantly

---

### Option C — GitHub Pages

1. Push this repo to GitHub
2. Go to **Settings → Pages**
3. Under **Source**, select **GitHub Actions**
4. The `.github/workflows/deploy.yml` handles everything on every push
5. Live at `https://yourusername.github.io/littleplan`

---

### Option D — Cloudflare Pages (best for global performance)

1. Go to [pages.cloudflare.com](https://pages.cloudflare.com) and sign up free
2. Click **"Create a project"** → **"Connect to Git"**
3. Select this repo
4. Set **Build output directory** to `public`
5. Leave build command empty (static site, no build step)
6. Click **Save and Deploy**

---

### Option E — Local / offline

```bash
# No install needed — just open the file
open public/index.html

# Or serve locally with any static server:
npx serve public
# → http://localhost:3000
```

---

## Adding Google AdSense (monetize with ads)

1. Apply at [adsense.google.com](https://adsense.google.com)
2. Once approved, replace the comment in `index.html`:

```html
<!-- Replace with your Google AdSense unit code -->
```

With your actual AdSense snippet:
```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-XXXXXXXXXXXXXXXX" crossorigin="anonymous"></script>
<ins class="adsbygoogle" style="display:block" data-ad-client="ca-pub-XXXXXXXXXXXXXXXX" data-ad-slot="XXXXXXXXXX" data-ad-format="auto"></ins>
<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>
```

AdSense typically pays $2–15 CPM (per 1,000 views) on parenting/family content.

---

## Adding AI (optional upgrade)

The tool works great without AI. When you're ready to add it:

### Using Cloudflare Workers AI (free tier covers ~12k users/month)

1. Create a `worker/index.js`:

```js
export default {
  async fetch(request, env) {
    if (request.method !== 'POST') return new Response('Method not allowed', { status: 405 });

    const { age, interests, season, time } = await request.json();

    const prompt = `You are a child development expert.
Generate a 7-day activity plan for a ${age}-year-old child.
Interests: ${interests.join(', ')}. Season: ${season}. Time per day: ${time} minutes.
Return ONLY valid JSON: { "theme": "string", "days": [{ "name": "string", "desc": "string", "materials": ["string"], "duration": number, "category": "creative|educational|outdoor|physical|sensory" }], "tips": ["string", "string", "string"] }`;

    const response = await env.AI.run('@cf/meta/llama-4-scout-17b-16e-instruct', {
      messages: [{ role: 'user', content: prompt }]
    });

    return new Response(response.response, {
      headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' }
    });
  }
};
```

2. In `app.js`, replace the `buildPlan()` call with a fetch to your worker endpoint
3. Fall back to the local library if the fetch fails

---

## Customisation tips

**Add more activities:** Edit `activities.js` — add entries to the `ACTIVITIES` array following the same format.

**Change the colour scheme:** Edit the CSS variables at the top of `styles.css`.

**Adjust the free tier limit:** Change `maxFree: 5` in `app.js`.

**Add a premium tier:** Integrate Stripe Checkout + set a cookie/token to bypass the daily limit.

---

## Tech stack

| What | Tool | Cost |
|------|------|------|
| Frontend | Plain HTML/CSS/JS | Free |
| Hosting | Vercel / Netlify / GitHub Pages / CF Pages | Free |
| Activity data | Local JS file (no database) | Free |
| Rate limiting | localStorage (client-side) | Free |
| Analytics | Google Analytics (optional) | Free |
| Ads | Google AdSense (optional) | You get paid |
| AI (optional) | Cloudflare Workers AI | Free up to 12k users/mo |

**Total monthly cost: $0** (until ~50k users/month, then ~$3/mo for AI only)

---

## License

MIT — free to use, modify, and sell.
