# Editing Guide

## Fastest Content Edits

Edit `data/content.js`.

- `slides`: title, kicker, bullets, proof points, and speaker notes.
- `resume`: the summarized CV tab.
- `questions`: questions to ask Puja.
- `chatResponses`: canned chat replies.
- `profile`: name, title, short bio, and profile image path.

Refresh `index.html` after saving.

## Profile Photo

The project currently uses:

`assets/profile.jpg`

To replace it later, overwrite that file or update this line in `data/content.js`:

```js
avatar: "assets/profile.jpg"
```

## Presentation Flow

Recommended interview sequence:

1. Open with slide 1: positioning and why this format exists.
2. Use slide 2 to show discovery and requirements gathering.
3. Use slide 3 to show how you translate ambiguity into a credible solution.
4. Use slide 4 to close with leadership, trust, and next-step questions.
5. Briefly switch to Classic mode to show adaptability and product fluency.
6. Use Demo Controls to show live configuration thinking.
7. End in the Resume tab with the concise positioning statement.

## Local Run

Open `index.html` directly in a browser, or run a simple local server:

```powershell
python -m http.server 4173
```

Then open:

`http://localhost:4173`
