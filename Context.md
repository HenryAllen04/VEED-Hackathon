# Context

This file captures **why** we are building the HelmGuard Product‑Led‑Growth widget the way we are, tying together the requirements of

* the **VEED Hackathon 2025** we are currently attending, and
* the **HelmGuard founding‑engineer take‑home challenge** that Henry must present early next week.

---

## 1 🌍 VEED Hackathon 2025 — Key Facts

| Item                    | Detail                                                                                           |
| ----------------------- | ------------------------------------------------------------------------------------------------ |
| **Venue**               | VEED HQ – 17‑18 Clere Street, Shoreditch, London EC2A 4LJ                                        |
| **Schedule**            | *Fri 30 May* 18:00 welcome → *Sun 1 Jun* 17:00 close; 36 h continuous hacking.                   |
| **Sponsor APIs**        | **VEED**, **fal.ai**, **ElevenLabs**, **Photoroom**, **Sieve** (must use VEED + fal for podium). |
| **Judging criteria**    | Creativity · Impact · Usability & Execution · Presentation (10 pts each).                        |
| **Prizes**              | 1st £5 k cash + large API credits; 2nd £2.5 k; 3rd £1 k.                                         |
| **Submission deadline** | *Sun 1 Jun 12:00* for code + demo video.                                                         |

### Hackathon design constraints

* **“Drop‑in” UX** favoured by Helmguard.
* Must visibly showcase **VEED** (avatar/video), **fal.ai** (LLM compute).
* Demo needs to run on site‑builder platforms (many judges’ sites are on Framer/Webflow).

---

## 2 🛠 HelmGuard Take‑Home Challenge — At a Glance

| Concern            | Requirement                                                                                                                                                                                       |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Deliverable #1** | *Lead‑generation strategy* for companies drowning in 300‑Q vendor questionnaires.                                                                                                                 |
| **Deliverable #2** | *Product‑led‑growth widget* that:<br>• accepts PDFs / Word / Excel & a question bank.<br>• auto‑answers via LLM / workflow automation.<br>• shows architecture, cost analysis & abuse mitigation. |
| **Meeting window** | Mon 2 Jun or Tue 3 Jun (evenings) to discuss solution.                                                                                                                                            |

### HelmGuard design constraints

* Public‑facing ➜ cost & rate‑limit analysis mandatory.
* Needs to prove *workflow automation* chops (RAG + stream answers).
* No local repo for marketing site (built in **Framer**); prefers **stand‑alone React bundle**.

---

## 3 🤝 Why the Selected Solution Serves *Both* Goals

| Goal                                  | How the widget addresses it                                                                                                                    |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Hackathon “Creativity + Impact”**   | A talking VEED avatar that answers security surveys live on the landing page is visually striking and directly demonstrates automation value.  |
| **Hackathon “Usability & Execution”** | 2‑line `<script>` snippet lets judges test in *their* browsers instantly; Shadow DOM prevents style clashes in the demo environment.           |
| **Sponsor API usage**                 | VEED (avatar container) + ElevenLabs (voice) + fal.ai (LLM) + Sieve (analytics) tick the mandatory & bonus sponsor boxes.                      |
| **HelmGuard PLG Demo**                | Shows low‑friction on‑page value, capturing lead emails after preview answers → aligns with lead‑gen deliverable.                              |
| **Architecture depth for interview**  | Repo includes FastAPI + RAG pipeline diagram, cost guardrails, usage analytics — exactly what Jack requested for the in‑office discussion.     |
| **Time efficiency**                   | Building once for the hackathon demo lets Henry reuse the same artifact (and recorded demo) for Monday’s interview, maximising week‑end hours. |

---

## 4 🔑 Hidden Benefits

* **Recruiter optics** – Demonstrates Henry can ship production‑ready, embedded SaaS components under deadline pressure.
* **Cross‑platform resilience** – Framer & Webflow both allow *Custom Code* blocks; snippet approach avoids CMS lock‑in.
* **Scalable cost profile** – fal.ai’s per‑second GPU billing + 5‑question preview caps run‑away spend while still impressing hackathon judges.

---

## 5 📎 Reference Links

* Hackathon guidebook (see `Hackathon Guidebook` message in Slack)
* Take‑home problem statement (email from Jack, 29 May)
* VEED → ElevenLabs avatar tutorial
* fal.ai ridgeline pricing chart

> *Any stakeholder can skim this file to understand **why** the Repo layout and build decisions in `Developer Guide.md` exist.*
