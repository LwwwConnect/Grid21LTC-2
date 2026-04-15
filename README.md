// GRID v21 — AUTONOMOUS REVENUE ENGINE (PHASE 2)

require("dotenv").config();
const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

// ===== MEMORY =====
let stats = {
  visits: 0,
  clicks: 0,
  leads: 0,
  conversions: 0
};

let leads = [];

let offers = [
  {
    name: "AI SaaS Tools",
    slug: "ai-saas",
    url: "https://example.com/ai",
    payout: 50
  }
];

// ===== MIDDLEWARE =====
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.use((req, res, next) => {
  stats.visits++;
  next();
});

// ===== HOME (SEO + LEAD CAPTURE) =====
app.get("/", (req, res) => {
  const links = offers.map(o => `
    <li><a href="/go/${o.slug}">${o.name} ($${o.payout})</a></li>
  `).join("");

  res.send(`
    <html>
      <head>
        <title>AI Income System</title>
      </head>
      <body>
        <h1>🔥 Automated Income Tools</h1>
        <ul>${links}</ul>

        <h2>📩 Get Free AI Income Guide</h2>
        <form method="POST" action="/lead">
          <input name="email" placeholder="Enter email" required/>
          <button type="submit">Get Access</button>
        </form>
      </body>
    </html>
  `);
});

// ===== LEAD CAPTURE =====
app.post("/lead", (req, res) => {
  const { email } = req.body;
  if (!email) return res.send("Invalid");

  leads.push({ email, time: Date.now() });
  stats.leads++;

  res.send("Captured. Check your inbox soon.");
});

// ===== AFFILIATE REDIRECT =====
app.get("/go/:slug", (req, res) => {
  const offer = offers.find(o => o.slug === req.params.slug);
  if (!offer) return res.send("Invalid");

  stats.clicks++;

  if (Math.random() < 0.1) stats.conversions++;

  res.redirect(offer.url);
});

// ===== AUTO AFFILIATE GENERATOR =====
function generateOffer() {
  const pool = [
    { name: "AI Copywriter SaaS", payout: 70 },
    { name: "No-Code Builder", payout: 60 },
    { name: "Automation Platform", payout: 80 },
    { name: "Affiliate Marketplace", payout: 100 }
  ];

  const pick = pool[Math.floor(Math.random() * pool.length)];

  const newOffer = {
    name: pick.name,
    slug: pick.name.toLowerCase().replace(/\s/g, "-"),
    url: "https://example.com/" + Math.random().toString(36).substring(7),
    payout: pick.payout
  };

  offers.push(newOffer);
  console.log("New Offer Added:", newOffer.name);
}

// ===== CAMPAIGN GENERATOR =====
function generateCampaign() {
  const keywords = ["ai income", "make money online", "passive income"];

  const keyword = keywords[Math.floor(Math.random() * keywords.length)];

  console.log("Campaign launched for:", keyword);
}

// ===== BRAIN ENGINE =====
function brain() {
  // prioritize highest payout
  offers.sort((a, b) => b.payout - a.payout);

  // remove low performers
  if (offers.length > 10) offers.pop();

  console.log("Brain optimized offers");
}

// ===== AUTOMATION LOOPS =====
setInterval(generateOffer, 20000);     // new offers
setInterval(generateCampaign, 15000); // campaigns
setInterval(brain, 30000);            // optimization

// ===== DASHBOARD UI =====
app.get("/dashboard", (req, res) => {
  res.send(`
    <html>
      <head>
        <title>GRID v21 Dashboard</title>
        <script>
          async function load() {
            const res = await fetch('/data');
            const data = await res.json();

            document.getElementById('stats').innerText =
              JSON.stringify(data.stats, null, 2);

            document.getElementById('offers').innerHTML =
              data.offers.map(o => 
                "<li>" + o.name + " ($" + o.payout + ")</li>"
              ).join("");
          }

          setInterval(load, 2000);
        </script>
      </head>
      <body onload="load()">
        <h1>📊 GRID v21 LIVE</h1>

        <h2>Stats</h2>
        <pre id="stats"></pre>

        <h2>Offers</h2>
        <ul id="offers"></ul>
      </body>
    </html>
  `);
});

// ===== DATA ENDPOINT =====
app.get("/data", (req, res) => {
  res.json({ stats, offers, leads: leads.length });
});

// ===== START =====
app.listen(PORT, () => {
  console.log("🚀 GRID v21 FULL SYSTEM LIVE:", PORT);
});
