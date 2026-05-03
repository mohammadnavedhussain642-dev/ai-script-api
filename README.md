import express from "express";
import cors from "cors";

const app = express();
app.use(cors());
app.use(express.json());

const OPENAI_API_KEY = process.env.OPENAI_API_KEY;

app.post("/generate", async (req, res) => {
  const { topic, duration, url } = req.body;

  const prompt = `
Create a viral short video script.

Topic: ${topic || "AI online income"}
Duration: ${duration} seconds

Structure:
Hook (3 sec)
Body
Final Verdict (CTA)

Include link: ${url || ""}
`;

  try {
    const response = await fetch("https://api.openai.com/v1/chat/completions", {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${OPENAI_API_KEY}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        model: "gpt-4o-mini",
        messages: [{ role: "user", content: prompt }],
        temperature: 0.9
      })
    });

    const data = await response.json();
    res.json({ script: data.choices[0].message.content });

  } catch (err) {
    res.status(500).json({ error: "Error generating script" });
  }
});

app.get("/", (req,res)=>{
  res.send("API Running 🚀");
});

app.listen(3000, () => console.log("Server running"));
