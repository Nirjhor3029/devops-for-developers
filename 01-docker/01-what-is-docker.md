# Chapter 1 — Docker ki, ar keno lage?

> **Course:** Docker (Zero to Hero) · **Chapter:** 1 of 8
> Website e porte chaile: [learn.bazardev.com/docker/01-what-is-docker](https://learn.bazardev.com/docker/01-what-is-docker)

Ei chapter porar age kichu install korte hobe na. Eita pure concept — কিন্তু এই concept clear না হলে পরের সব chapter confusing lagbe। Tai ektu dhorjo niye poro, example gulo mon diye bojho.

## 1. Problem ta ki, Docker er age

Ei situation ta onek developer er sathe hoyeche:

- Tumi nijer laptop e app banailo, shob kichu perfect kaj kortese.
- Server e deploy korar por — **error**. "Works on my machine" কিন্তু server e chalena।
- Karon: tomar laptop e PHP 8.3, server e PHP 8.1. Tomar laptop e ekta library install kora, server e nai. Tomar OS Ubuntu, teammate er Windows.

Eita k bola hoy **"it works on my machine" problem** — software development er shobcheye common ar frustrating problem.

Age er solution gulo:
- **Virtual Machine (VM)** — pura ekta virtual computer banano, full OS shoho. Kaj kore, কিন্তু onek heavy — ekta VM e 2-4 GB RAM lage, boot hote minutes lage, ar ekta server e 5-6 ta VM চালালে resource শেষ হয়ে যায়।
- **Detailed setup document** — "ei ei step follow koro server setup korte" — but manual, error-prone, ar time e update na hole outdated hoye jay.

Docker ei problem ta solve kore onno bhabe — **lightweight, fast, ar guaranteed consistent**।

## 2. Container ki (concept)

**Container** holo ekta hocche app ke "package" kora — code, dependencies (library, runtime version), configuration — shob kichu ekta box er moddhe bondi kore deya, jate shei box jekhanei (laptop, server, cloud) chalano hok, **exactly same behavior** dekhay।

Simple analogy: **shipping container**। Age jokhon jahaj e maal pathano hoto, har jinis er shape-size alada hoto, loading-unloading onek shomoy lagto. Tarpor ekta standard-size **shipping container** ashlo — jei jinis e thak (cloth, electronics, food), shob same-size box e bhore deya hoy। Jahaj, truck, train — jekono vehicle ekই box handle korte pare, karon box er shape fixed। Docker container o tai kore software er jonno — **jekono jinis hok, ekই "box" e package hoy, tai jekhanei run koro, same way kaj kore।**

### VM vs Container — ki different?

| | Virtual Machine | Container |
|---|---|---|
| Full OS lage? | Haa, prottekta VM e full guest OS | Na, host machine er OS kernel share kore |
| Boot time | Minutes | Seconds (kখনো কখনো < 1 second) |
| Size | GBs (full OS + app) | MBs (shudhu app + dependencies) |
| Isolation level | Strong (hardware-level virtualization) | Process-level (lightweight, but kম isolated than VM) |
| Ekta server e koyta chalano jay | Kom (5-10 ta, resource heavy) | Onek (shundor kore 100+ container o chalano jay) |

**Important bhul dharona:** Container mane "mini VM" na। Container asholey host machine er **ekই OS kernel** use kore — শুধু process gulo ke ekta isolated "bubble" e rakhe (nijer filesystem, network, process list dekhe, kintu kernel share kore)। Ei jonno e container eto lightweight ar fast.

## 3. Docker ta ashole ki?

"Container" holo ekta **general concept** (Linux kernel er feature — namespaces + cgroups diye banano) — eita Docker invent kore nai। কিন্তু container ta **easily use korar jonno tool** banaise Docker — ar eita eto shundor bhabe korese je "container" mane ekhon shobai "Docker" bojhe।

Docker asholey kayekta part niye gothito:

- **Docker Engine** — background e running ekta service (`dockerd`), যেটা actually container banay/chalay/bondho kore।
- **Docker CLI** — tumi terminal e `docker run`, `docker build` command dile, eita client hishebe Engine ke bole "eita koro"।
- **Docker Image** — ekta read-only "blueprint" / "snapshot" — tomar app + dependencies + OS files er ekta package। (Bistarito 3rd chapter e।)
- **Docker Container** — ekta image ke "run" korle jei live, running instance ta pao, oitai container। (Same image theke multiple container run kora jay।)
- **Docker Hub** — online ekta registry (jemon GitHub, kintu code er bodole images er jonno) — jekhan theke readymade image download (`pull`) kora jay, ar nijer image upload (`push`) o kora jay।

### Easy bhabe bolte gele:

> **Image = recipe (likhito instructions + ingredients)**
> **Container = oi recipe diye banano, plate e deya ready khabar**

Ekই recipe (image) diye jotobar e khabar (container) banao, taste shomoy ekই hobe — predictable, consistent।

## 4. Docker er architecture — kivabe kaj kore

```
 ┌──────────────┐        commands       ┌───────────────────┐
 │  Docker CLI   │ ────────────────────▶ │   Docker Daemon     │
 │ (docker run..)│                       │     (dockerd)        │
 └──────────────┘                       └─────────┬───────────┘
                                                     │
                            ┌────────────────────────┼─────────────────────────┐
                            ▼                        ▼                         ▼
                     ┌─────────────┐          ┌─────────────┐           ┌─────────────┐
                     │  Image A     │          │  Container 1 │           │  Container 2 │
                     │ (blueprint)  │ ───run──▶│ (running)    │           │ (running)     │
                     └─────────────┘          └─────────────┘           └─────────────┘
                            ▲
                            │ pull / push
                     ┌─────────────┐
                     │ Docker Hub   │  (registry — internet e images er store)
                     └─────────────┘
```

Flow ta eki rokom:
1. Tumi CLI e command dao (jemon `docker run nginx`)।
2. CLI eita Daemon ke pathay।
3. Daemon check kore image ta local e ache naki — na thakle Docker Hub theke **pull** kore niye ashe।
4. Image theke ekta **container** start hoy — eita ekta running process, isolated bhabe।

## 5. Keno companies/developers Docker use kore

- **Consistency** — "works on my machine" problem shesh। Dev, staging, production — sob jaygay ekই container chalay, tai behavior same.
- **Fast setup** — notun teammate join korleo, `docker compose up` dile shob service (app, database, cache) ekshathe up hoye jay — manual install documentation lagena.
- **Microservices friendly** — ekta boro app ke chotto chotto service e vag kore, prottekta nijer container e run korano shohoj (payment service, auth service, notification service — alada alada container).
- **CI/CD er jonno perfect** — automated testing/deployment pipeline e, container use kore guarantee deya jay je test ar production environment ekই.
- **Resource efficient** — VM er theke onek kom resource e beshi service chalano jay, tai hosting cost o kome.
- **Easy rollback** — notun version e problem hole, purono image er container e firey jawa onek shohoj.

## 6. Ei course e ami ja shikhbo (roadmap preview)

| Chapter | Topic |
|---|---|
| 1 | Docker ki, ar keno lage ✅ (eita) |
| 2 | Install + first container run kora |
| 3 | Images vs Containers — commands hands-on |
| 4 | Dockerfile — nijer image lekha |
| 5 | Volumes & Networking — data persist kora, container-to-container communication |
| 6 | Docker Compose — multi-container app (Laravel + MySQL + Redis) ekshathe chalano |
| 7 | Best practices + debugging |
| 8 | Real project — nijer Laravel app ke Dockerize kora (end to end) |

## 7. Key Takeaways

- Container = app + dependencies + config, ekta portable "box" e package kora।
- VM full OS virtualize kore (heavy), container OS kernel share kore (light, fast)।
- Docker = container banano/chalano/manage korar jonno sobcheye popular tool (Engine + CLI + Image + Container + Hub)।
- Image = blueprint (static), Container = running instance (live)।
- Main benefit: **consistency** — jekhanei chalao, same behavior।

## 8. Nijeke test koro (exam prep)

Ei question gulo nijeke jiggesh koro — answer na parle, upore oi section ta re-read koro।

<details>
<summary><strong>Q1. VM ar Container er mul difference ki?</strong></summary>

VM pura ekta separate guest OS virtualize kore (heavy, slow boot), container host OS er kernel share kore shudhu process-level isolation kore (light, fast boot)।
</details>

<details>
<summary><strong>Q2. Image ar Container er difference ki?</strong></summary>

Image holo static blueprint/snapshot (recipe), Container holo oi image theke start kora running instance (ready khabar)। Ekই image theke multiple container run kora jay।
</details>

<details>
<summary><strong>Q3. Docker Hub ta ashole ki?</strong></summary>

Eita ekta online registry (GitHub er moto, kintu image er jonno) — jekhan theke readymade image pull kora jay, ar nijer banano image push kore rakha jay।
</details>

<details>
<summary><strong>Q4. "Works on my machine" problem Docker kivabe solve kore?</strong></summary>

Karon Docker container e app er sathe dependencies/config shob kichu ekই package e thake — tai dev, staging, production jekhanei chalano hok, exactly ekই environment paoa jay, OS/library version mismatch er shomvabona thake na।
</details>

## 9. Free resources (English — chaile dekho, must na)

- Official docs: [Docker Get Started guide](https://docs.docker.com/get-started/)
- YouTube: **"Docker Tutorial for Beginners [FULL COURSE in 3 Hours]"** — channel: *TechWorld with Nana*
- YouTube: **"Docker Tutorial for Beginners - A Full DevOps Course"** — channel: *freeCodeCamp.org*

---

**Next:** [Chapter 2 — Install + First Container →](../01-docker/02-install-and-first-container.md) *(coming soon)*
