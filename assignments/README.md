# How to submit homework

You submit homework with a **fork** and a **pull request**, the same way people contribute to real open-source projects.

You work **offline** in the lab and **push from home**.

---

## Before your first homework (once only)

### 1. Create a GitHub account
Go to **github.com** and sign up (free).

### 2. Tell Git who you are
```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

### 3. Fork the course repository (needs internet)
1. Open **github.com/Hu8MA/hilla-ai-nlp-2026**
2. Click **Fork** (top right) → **Create fork**.

You now have your own copy: `github.com/<your-username>/hilla-ai-nlp-2026`

### 4. Clone **your fork** to your computer
```bash
git clone https://github.com/<your-username>/hilla-ai-nlp-2026.git
cd hilla-ai-nlp-2026
```

---

## For each homework

The example below is for **HW1**. For other homework, change `hw1` to `hw2`, `hw3`, and so on.

### 1. Update your fork (needs internet)
New homework is added to the course repository each week, so your fork must be up to date.
1. On **your fork** on github.com, click **Sync fork** → **Update branch**.
2. On your computer:
```bash
git checkout main
git pull
```

### 2. Create a branch for this homework
```bash
git checkout -b hw1
```

### 3. Create your answer file (offline)
1. Read the questions in `assignments/hw1.md`.
2. Create your file: `submissions/hw1/<your-github-username>.md`
3. Write your answers in it.

### 4. Save your work with commits (offline)
```bash
git add submissions/hw1/<your-github-username>.md
git commit -m "HW1: answer Q1"
```
Commit after each question, with a clear message.

### 5. Push your branch (needs internet)
```bash
git push -u origin hw1
```

### 6. Open a pull request
1. Open your fork on github.com. You will see a yellow bar: **"hw1 had recent pushes"**.
2. Click **Compare & pull request**.
3. Title: `HW1 - <your-github-username>`
4. Click **Create pull request**.

**Your submission is the pull request.** The time the pull request is opened is your submission time.

### 7. Fix something after submitting?
Commit and push again to the same `hw1` branch, before the deadline. The pull request updates automatically.

---

## Rules

- **Deadline:** the day **before** the next lecture, 11:59 PM. The pull request must be **opened** before the deadline.
- Change **only your own file**. Pull requests that touch other files will not be accepted.
- Use your **GitHub username** only. Do **not** write your full name or university ID in the file: the repository is public.
- All submissions are public. Copied answers are easy to spot and get **zero**.

---

## Feedback

The lecturer comments on your pull request and then **merges** it. After the merge, your name appears in the course repository's **Contributors** list. 🎉
