module.exports = async function handler(req, res) {
  const apiKey = (process.env.GEMINI_API_KEY || "").trim();

  if (req.method === "GET") {
    return res.status(200).json({
      ok: true,
      hasApiKey: Boolean(apiKey),
      message: apiKey ? "JARVIS online and key loaded." : "JARVIS online, GEMINI_API_KEY missing."
    });
  }

  if (req.method !== "POST") return res.status(405).json({ error: "Method not allowed" });
  if (!apiKey) return res.status(500).json({ error: "Missing GEMINI_API_KEY in Vercel Environment Variables." });

  try {
    const body = typeof req.body === "string" ? JSON.parse(req.body || "{}") : (req.body || {});
    const message = typeof body.message === "string" ? body.message.trim() : "";
    const history = Array.isArray(body.history) ? body.history : [];
    const memories = Array.isArray(body.memories) ? body.memories : [];
    const clientTime = typeof body.clientTime === "string" ? body.clientTime : new Date().toUTCString();

    if (!message) return res.status(400).json({ error: "Message cannot be empty." });
    if (message.length > 4000) return res.status(400).json({ error: "Message too long (max 4000 chars)." });

    const memText = memories.length
      ? `\nSaved facts about Aryan:\n- ${memories.slice(0, 30).join("\n- ")}`
      : "\nNo saved facts yet.";

    const systemPrompt = `You are JARVIS, a personal AI assistant created for your owner, Aryan.
Local time for Aryan: ${clientTime}

Personality:
- Intelligent, calm, logical, sophisticated, helpful, and occasionally dryly witty (Iron Man JARVIS vibe without overdoing movie quotes).
- Address Aryan naturally. Keep answers concise and conversational so they sound natural when spoken aloud, while giving clear step-by-step detail when asked.

Honesty rule:
- Right now in Phase 1, you CANNOT browse the live internet, set iOS reminders, run Apple Shortcuts, control smart home devices, or inspect images. Never fake these; honestly say they are planned for future phases.

Memory rule:${memText}
- ONLY if Aryan explicitly asks you to remember something or states a clear preference/fact (e.g. "Remember I live in India"), add one line at the very end:
[[MEMORY: concise fact]]`;

    const contents = [];
    for (const item of history.slice(-14)) {
      if (!item || typeof item.text !== "string" || !item.text.trim()) continue;
      const role = item.role === "model" ? "model" : "user";
      if (contents.length === 0 && role !== "user") continue;
      if (contents.length > 0 && contents[contents.length - 1].role === role) continue;
      contents.push({ role, parts: [{ text: item.text.trim() }] });
    }
    if (contents.length > 0 && contents[contents.length - 1].role === "user") contents.pop();
    contents.push({ role: "user", parts: [{ text: message }] });

    const payload = {
      systemInstruction: { parts: [{ text: systemPrompt }] },
      contents,
      generationConfig: { maxOutputTokens: 1500 }
    };

    const models = ["gemini-3.8-flash", "gemini-3.5-flash-lite", "gemini-3.6-flash", "gemini-3.1-flash-lite"];
    let data = null;
    let lastStatus = 500;
    let lastDetail = "";

    for (const model of models) {
      const r = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/${model}:generateContent`, {
        method: "POST",
        headers: { "Content-Type": "application/json", "x-goog-api-key": apiKey },
        body: JSON.stringify(payload)
      });
      if (r.ok) { data = await r.json(); break; }
      lastStatus = r.status;
      try { lastDetail = (await r.json())?.error?.message || ""; } catch { lastDetail = ""; }
      if ([400, 404, 429, 503].includes(r.status)) continue;
      break;
    }

    if (!data) {
      if (lastStatus === 429) return res.status(429).json({ error: "Free Gemini quota reached. No charges made—try again shortly." });
      return res.status(502).json({ error: `Gemini error (${lastStatus}): ${lastDetail || "Check GEMINI_API_KEY in Vercel."}` });
    }

    const raw = data.candidates?.[0]?.content?.parts?.map(p => p.text || "").join("").trim() || "";
    if (!raw) return res.status(500).json({ error: "Gemini returned an empty response." });

    const newMemories = [];
    const reply = raw.replace(/\[\[MEMORY:\s*(.+?)\]\]/gi, (_, f) => {
      if (f.trim()) newMemories.push(f.trim());
      return "";
    }).trim();

    return res.status(200).json({ reply: reply || "Understood, Aryan.", newMemories });
  } catch (e) {
    return res.status(500).json({ error: `Server error: ${e.message}` });
  }
};
