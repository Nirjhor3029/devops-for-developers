# learn.bazardev.com: Project Context

Written 2026-11-17 so a new person or a new AI session can understand the whole project in one read. It covers only **learn.bazardev.com** (the learning site and its course content), not the owner's other sites. No secrets are in this file. (Kept in the content repo's root; it is not a chapter and is not shown on the website.)

---

## 1. What this project is, and why it exists

**DevOps Shikhbo** ("Learn DevOps") is a free, structured, zero-to-hero DevOps curriculum for developers in Bangladesh and beyond, written to be read in **English and Bangla**, with diagrams, quizzes, a final exam per course, and a certificate.

- **Audience:** a developer (mainly Laravel/PHP) who can code but has never run a server, a pipeline or infrastructure.
- **Goal:** take that person from "I have never used a terminal" to "I can run a Laravel app in production, repeatably and safely".
- **Principles:** explain simply; build explicitly on earlier chapters; use **real Laravel projects** as the running example; be **honest** about what was and was not executed; reading is always free and public; every claim comes from official documentation.
- **Owner:** Nirjhor. He works task by task, in **Banglish** (Bangla in English letters). Replies to him are in Banglish.

## 2. Two repositories, one site

| Repo | What it holds | Location |
|---|---|---|
| **devops-for-developers** | The **English** course content: one Markdown file per chapter, SVG diagrams, README. Readable directly on GitHub. | `/var/www/dev/projects/devops-for-developers` |
| **learn.bazardev.com** | The **Laravel website** that renders that content, plus everything Bangla, quizzes, exams, accounts and progress. | `/var/www/dev/projects/learn.bazardev.com` |

The website reads the English Markdown from the sibling repo (`config/course.php` → `content_path`, overridable with `COURSE_CONTENT_PATH`). **Editing a content file changes the live site immediately**, before any commit or push. Chapter images are SVGs referenced by raw GitHub URLs, so they appear after the content repo is pushed.

## 3. The website (technical)

- **Stack:** Laravel 13 (PHP 8.3+), Blade views, Tailwind, MySQL (Percona) in production, Caddy web server. Tests use in-memory SQLite.
- **Content flow:** `config/course.php` lists courses and chapters (the source of truth for order, titles, status and prerequisites) → `CourseContent` service renders Markdown → views show it. `?lang=bn` switches to the Bangla file.
- **Key code:** `App\Services\{CourseContent, Learning, Quiz, Exam}`; controllers for courses, learning (done/bookmark/notes), quiz, exam, certificate, contributions, account, admin; models `User, ChapterState, Note, Contribution, QuizAttempt, Certificate`.
- **Features (all built):**
  - Public reading, always free, no login.
  - Accounts: register, login, password reset, email verification, `/account`; admin **manually approves** accounts.
  - **Progress:** mark chapter done, bookmarks, "continue where you left off", progress bars, `/my-learning` dashboard, private per-chapter notes.
  - **Contributions:** readers submit a resource, tip or mistake report → admin queue → approved items show under the chapter with the sender's name. Mistake reports are never public.
  - **Quiz:** 5 MCQs per chapter (EN + BN, each with a `why` explanation).
  - **Final exam:** a pool of 32 questions per course, 20 drawn at random per sitting, pass mark 16/20, opens only after every chapter quiz of the course is passed. A failed sitting shows chapters to re-read, never the right answers.
  - **Certificate:** issued only after passing the exam; public verify page `/certificate/{code}` (noindex, printable A4); honest "course completion" wording.
  - **Admin:** `/admin/users`, `/admin/contributions`, `php artisan app:make-admin`.
  - **Learning path:** home page numbers the courses, each course page shows "Take these first: ...".
- **Tests:** 9 test files, 108 tests (106 pass, 2 skip by design). Run `php artisan test`. Never commit with a failing test.

## 4. How the courses are structured

### 4.1 The learning path (config order is what counts)

Courses are ordered so nothing needs something taught later. Each course has a `requires` list in `config/course.php`.

| # | Slug | Course | Needs first |
|---|---|---|---|
| 1 | `linux-shell` | Linux & Shell Basics | nothing |
| 2 | `git-github` | Git & GitHub (Advanced) | Linux |
| 3 | `docker` | Docker | Linux, Git |
| 4 | `cicd` | CI/CD (GitHub Actions) | Git, Docker |
| 5 | `cloud` | Cloud (AWS / VPS basics) | Linux, CI/CD |
| 6 | `monitoring` | Monitoring & Logging | Linux, Cloud |
| 7 | `iac` | Infrastructure as Code & Ansible | Linux, Git, Cloud |
| 8 | `terraform` | Terraform | IaC, Docker |
| 9 | `kubernetes` | Kubernetes (advanced, last of the core path) | Docker, Git, CI/CD |

### 4.2 Every course has the same shape

- **8 chapters**, the last always a **real project** (a Laravel app) that uses everything before it.
- **8 chapter quizzes** (5 MCQ each) and **one final exam** (32-question pool, 4 per chapter, each tagged with its chapter).
- **English + Bangla** versions of every chapter.

Totals today: **9 courses, 72 chapters, 72 quizzes (360 questions), 9 exams (288 questions), all with Bangla.**

### 4.3 Every chapter has the same shape

1. A header block (course, chapter n of 8, links to the website and the Bangla version).
2. An **honesty note**: which tools were not installed or run on the writing server, what was checked from official docs, and that sample output is illustrative. No chapter claims to have run something it did not.
3. An SVG diagram, numbered sections with small examples, a "good habits" list.
4. A **self-check** of 8 questions with answers in `<details>` blocks.
5. Free resources (official docs only) and a "Next" link.

**Bangla rule:** the Bangla chapter is a faithful translation, English technical terms kept as they are, and **every code block identical** to the English one (checked by script).

## 5. Where everything lives

| Thing | Path |
|---|---|
| English chapters + SVGs | `devops-for-developers/<NN-course>/<NN-chapter>.md`, `.../images/*.svg` |
| Bangla chapters | `learn.bazardev.com/resources/content/bn/<NN-course>/<NN-chapter>.md` |
| Chapter quizzes | `learn.bazardev.com/resources/content/quiz/<course-slug>/<chapter-slug>.json` |
| Final exams | `learn.bazardev.com/resources/content/exam/<course-slug>.json` |
| Course/chapter list, order, status, prerequisites | `learn.bazardev.com/config/course.php` |
| Learning path tests | `learn.bazardev.com/tests/Feature/LearningPathTest.php` (also `ExamTest.php`, others) |

Folder numbers (`01-docker` ... `09-terraform`) are **historical, not the order**. Do not rename them or any slug: 69 chapter files embed GitHub image URLs with the folder names, and progress, quizzes and certificates are keyed by `course slug + chapter slug`.

Quiz JSON: `[{q:{en,bn}, options:[{en,bn}×4], answer:index, why:{en,bn}}]`. Exam JSON is the same without `why`, plus `ref` (the chapter slug). Adding the files is enough: the exam card, locks and certificate appear automatically.

## 6. How a chapter is made (the workflow that works)

One chapter at a time, owner reviews each step:

1. Read the **official documentation** for every command and claim (and Ubuntu package search for package names). Anything not found in the docs is not written as fact; it is marked as an assumption or left out.
2. Write the English chapter and its SVG. Validate: SVG parses, every YAML block parses, code blocks balanced, every external link returns 200, no real-looking secrets (examples use fake placeholders).
3. Write the Bangla version (code blocks identical).
4. Write 5 quiz questions with an explicit answer index. **Print every question with its marked answer and read it against the chapter** before committing (swapping right/wrong has happened several times).
5. Flip the chapter to `available` in `config/course.php`, fix the previous chapter's "Next" link in Bangla.
6. Run `php artisan test`, check the live pages return 200, then commit and push both repos (only when told to).
7. After chapter 8, write the course exam (32 questions) and verify the same way.

Commits end with the two attribution lines the harness gives. Never commit `.env` or anything with secrets.

## 7. Status today

- **Core path: complete.** All 9 courses, 72 chapters, quizzes, exams, Bangla: live.
- **Nothing is `coming_soon`** in `config/course.php` right now.
- The tools themselves were **not executed** for the courses (Terraform, Ansible, Prometheus and others are not installed on the writing server, by the owner's choice: "just teach, install nothing"). Chapters say so openly. The owner has not yet reviewed every chapter's content in detail.

## 8. Planned next: the Advanced part

The owner asked for a separate **Advanced** part, built course by course. The site will show two sections: **Core path (1–9)** and **Advanced (own numbering)**, via a `section` key per course in `config/course.php` (`core` / `advanced`) and a grouped home page. Each new course starts `coming_soon` with no chapters, then is written chapter by chapter, same quality bar. **Not started yet** (waiting on the harness guard, see section 10).

Agreed four, in dependency order:

| Adv. | Course | Needs first |
|---|---|---|
| A1 | **Networking & Web Servers** (DNS, TLS, Caddy/Nginx, reverse proxy, load balancer basics) | Linux, Cloud |
| A2 | **Linux Server Security & Secrets** (SSH hardening, firewall, fail2ban, secrets management, least privilege) | Linux, Cloud, Networking |
| A3 | **Database Backup & Recovery** (MySQL backup, restore tests, replication intro, RPO/RTO) | Linux, Cloud |
| A4 | **Laravel Production Deploy** (zero-downtime, queue workers, scheduler, rollback) | CI/CD, Cloud, IaC, Networking, Backup |

**Proposed, awaiting the owner's final OK:** a fifth, **A5 Scaling & High Availability**, placed last, for what the current curriculum only touches by name: load balancer setup and health checks, the same code on many servers, stateless apps, shared state (sessions, cache, queue via Redis), shared storage for uploads, database replication and failover, rolling and blue-green deploys across servers, autoscaling ideas, and a real project with a 3-node Laravel cluster behind a load balancer. (Today these appear only inside Kubernetes, Ansible `serial`, Terraform `count`/`for_each` and the Cloud overview; horizontal scaling and shared sessions appear nowhere.)

Further topics to consider later: scripting (Bash/Python automation), SRE practice (SLO/SLI, incident response, postmortems), GitOps/Helm after Kubernetes, deeper observability (OpenTelemetry tracing), FinOps.

## 9. Other open items and decisions

- **Not yet approved (Phase 4 ideas):** email to admin on a new submission, GitHub login, public contributors page, notes export, public discussion.
- **SMTP:** mail is on the `log` driver until the owner adds credentials; the admin approves accounts manually.
- **Owner decisions on record:** email is shown publicly only if the user opts in; "comments" currently means private notes; the owner declined a **database backup setup for this site** earlier ("do not set one up unless asked again"; the planned Backup course only *teaches* backups, it does not change this).
- **Housekeeping noted earlier:** Terms/Privacy pages, rotating admin passwords, log rotation. Check whether still open before acting.

## 10. Working rules for anyone (or any AI) touching this project

- Talk to the owner in **Banglish**. Do one step at a time and show the result of each step.
- **Ask before** destructive commands (`rm -rf`, `migrate:fresh`, `git reset --hard`, force push, dropping data) and before any `sudo` (say what, and why). Plan and wait for OK before installing or changing configuration.
- Never commit secrets; never print them. Examples in chapters use fake placeholders.
- Commit and push only when asked. Gate commits on a green `php artisan test`.
- Do not rename slugs or content folders. Keep English and Bangla in step.
- The live site serves the working tree directly: a new migration needs `php artisan migrate --force` right after the code that uses it; after adding Tailwind classes run `npm run build` and `php artisan view:clear`.
- Known environment quirks (MySQL socket login, `laravel.log` permissions, headless browser check, isolated test server) are in the server-gotchas memory note.
- **Harness guard seen on this project:** a background session may be blocked from editing files in the learn.bazardev.com repo until it works in a git worktree. Only the owner can authorise that (saying "worktree"), or switch the guard off in `.claude/settings.json` (`"worktree": {"bgIsolation": "none"}`). In a worktree, tests need a real copy of `vendor` and `public/build`, an `APP_KEY` and `COURSE_CONTENT_PATH` set for the run, and git commands must not target other checkouts. The devops-for-developers repo is not affected.
