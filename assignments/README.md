# How to submit homework

Each homework has its own folder:

```
assignments/
├── README.md        ← this guide
├── hw-1/
│   ├── README.md    ← the questions
│   ├── student-a.ipynb
│   └── student-b.ipynb
├── hw-2/
└── ...
```

You answer in **one Jupyter notebook**, named with your GitHub username, inside the homework folder. You submit it with a **fork** and a **pull request**, the same way people contribute to real open-source projects.

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

### 4. Clone **your fork** to your computer
```bash
git clone https://github.com/<your-username>/hilla-ai-nlp-2026.git
cd hilla-ai-nlp-2026
```

---

## For each homework

The example is for **HW1**. For other homework, change `hw-1` to `hw-2`, `hw-3`, and so on.

### 1. Update your fork (needs internet)
New homework is added each week, so your fork must be up to date.
1. On **your fork** on github.com, click **Sync fork** → **Update branch**.
2. On your computer:
```bash
git checkout main
git pull
```

### 2. Create a branch for this homework
```bash
git checkout -b hw-1
```

### 3. Write your answers (offline)
1. Read the questions in `assignments/hw-1/README.md`.
2. Create a notebook **in the same folder**, named with your GitHub username:
   `assignments/hw-1/<your-github-username>.ipynb`
3. Write your answers:
   - Written answers → **Markdown** cells
   - Code → **Code** cells
4. Before saving: **Kernel → Restart & Run All**, and check there are no errors.

### 4. Save your work with a commit (offline)
```bash
git add assignments/hw-1/<your-name>.ipynb
git commit -m "HW1: <your-name>"
```

```bash
`for ex`
git add assignments/hw-1/hussein-mahdi.ipynb
git commit -m "HW1: hussein mahdi"
```
### 5. Push your branch (needs internet)
```bash
git push -u origin hw-1
```

### 6. Open a pull request
1. Open your fork on github.com. You will see a yellow bar: **"hw-1 had recent pushes"**.
2. Click **Compare & pull request**.
3. Title: `HW1 - <your-name>`
4. Click **Create pull request**.

**Your submission is the pull request.** The time the pull request is opened is your submission time.

### 7. Fix something after submitting?
Commit and push again to the same `hw-1` branch, before the deadline. The pull request updates automatically.

---

## Rules

- **Deadline:** the day **before** the next lecture, 11:59 PM. The pull request must be **opened** before the deadline.
- Submit **one notebook only**, named `<your-github-username>.ipynb`. Pull requests that change any other file will not be accepted.
- Use your **GitHub username** only. Do **not** write your full name or university ID: the repository is public.
- All submissions are public. Copied answers are easy to spot and get **zero**.

---

## Feedback

The lecturer reviews your pull request. If it is correct, it is **merged**, and your name appears in the course repository's **Contributors** list. 🎉
If something needs fixing, you get a comment on the pull request.
