# How to submit homework

All homework is submitted through **GitHub Classroom**. You get your own **private repository** for each homework: only you and the lecturer can see it.

You work **offline** in the lab and **push from home**.

---

## Before your first homework

1. Create a free account at **github.com** (if you don't have one).
2. Install Git on your computer (done in the week 1 lab).
3. Tell Git your name and email (once only):
```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

---

## For each homework

### 1. Accept the assignment (needs internet)
1. Open the invitation link shared by the lecturer.
2. The first time only: choose **your university ID** from the list.
3. Click **Accept this assignment**.
4. Wait a few seconds, then open your new repository.

### 2. Clone it to your computer (needs internet)
Click the green **Code** button, copy the link, then run:
```bash
git clone <the-link-you-copied>
```

### 3. Write your answers (offline)
Open `answers.md` and write your answers under each question.

### 4. Save your work with commits (offline)
```bash
git add answers.md
git commit -m "Answer Q1"
```
Commit after each question, with a clear message.

### 5. Push to GitHub (needs internet)
```bash
git push
```
Open your repository on github.com and check that your answers are there.

---

## Rules

- **Deadline:** the day **before** the next lecture, 11:59 PM.
- Only work **pushed** before the deadline is graded. A commit on your laptop that you didn't push does not count.
- Write your answers yourself. Copied answers get zero.

---

## Feedback

The lecturer writes comments in the **Pull requests → Feedback** tab of your repository. Check it after grading.