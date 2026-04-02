# Gift-app
Confused for what to buy in occasion like festival, birthday etc, then this is the app for you add your budget, occasion, and the person and we'll generate the perfect gift for you using AI
// 🚀 FINAL READY-TO-LAUNCH APP
// 👉 You ONLY need to paste your API key in Vercel

// ================= FRONTEND =================

import { useState } from "react";

export default function GiftRandomizer() {
  const [gift, setGift] = useState(null);
  const [loading, setLoading] = useState(false);
  const [budget, setBudget] = useState(1000);
  const [occasion, setOccasion] = useState("Birthday");
  const [person, setPerson] = useState("Friend");

  const generateGift = async () => {
    setLoading(true);
    setGift(null);

    try {
      const res = await fetch("/api/generate-gift", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ budget, occasion, person }),
      });

      const data = await res.json();
      setGift(data);
    } catch (err) {
      setGift({ clue: "Error generating gift. Check setup.", link: "#" });
    }

    setLoading(false);
  };

  return (
    <div style={{ padding: 20, textAlign: "center" }}>
      <h1>🎁 AI Gift Finder</h1>

      <input
        placeholder="Budget (₹)"
        value={budget}
        onChange={(e) => setBudget(e.target.value)}
      />

      <br /><br />

      <select onChange={(e) => setOccasion(e.target.value)}>
        <option>Birthday</option>
        <option>Anniversary</option>
        <option>Festival</option>
      </select>

      <br /><br />

      <select onChange={(e) => setPerson(e.target.value)}>
        <option>Friend</option>
        <option>Girlfriend</option>
        <option>Parents</option>
      </select>

      <br /><br />

      <button onClick={generateGift}>
        {loading ? "Thinking..." : "Generate Gift"}
      </button>

      {gift && (
        <div style={{ marginTop: 20 }}>
          <p><b>💰 Budget:</b> ₹{budget}</p>
          <p><b>🎉 Occasion:</b> {occasion}</p>
          <p><b>👤 For:</b> {person}</p>

          <h3>{gift.clue}</h3>

          <a href={gift.link} target="_blank">
            🎁 View Best Deal
          </a>
        </div>
      )}

      <footer style={{ marginTop: 40 }}>
        <p>© AI Gift Finder</p>
      </footer>
    </div>
  );
}


// ================= BACKEND =================
// File: /pages/api/generate-gift.js

export async function POST(req) {
  const { budget, occasion, person } = await req.json();

  const prompt = `Suggest 1 unique gift for a ${person} for ${occasion} under ₹${budget}. Short idea only.`;

  const response = await fetch("https://api.openai.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${process.env.OPENAI_API_KEY}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      model: "gpt-4o-mini",
      messages: [{ role: "user", content: prompt }]
    })
  });

  const data = await response.json();

  const clue = data.choices?.[0]?.message?.content || "Gift idea";

  const search = encodeURIComponent(clue);

  const link = `https://www.amazon.in/s?k=${search}&tag=YOURTAG-21`;

  return Response.json({ clue, link });
}


// ================= WHAT YOU DO =================

// 1. In Vercel → add:
// OPENAI_API_KEY = your_key

// 2. Replace YOURTAG-21 with your Amazon ID

// 3. Deploy

// DONE ✅

// 💰 Your app will now:
// - Generate AI gifts
// - Show budget details
// - Send users to Amazon
// - Earn commissions
