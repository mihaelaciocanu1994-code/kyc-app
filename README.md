# Nørregade Revision · KYC app

A simple KYC (customer due diligence) tool for the KYC Onboarding Case. It is one file, `index.html`, and runs entirely in the browser. Nothing is sent to a server: client data is saved in the browser (localStorage), and the sanctions list is read from a file you load yourself.

## How to use it

1. Open the app and click **Create K01–K10**.
2. Click **Load targets.simple.csv** and pick the EU Financial Sanctions file you downloaded from OpenSanctions. It is remembered in the browser after the first load.
3. For each client, fill in the tabs:
   - **Identity**: name, type, CVR, address, country, industry, date of birth and nationality (persons), documents received, inconsistencies.
   - **Owners & management**: every person and company in the folder, with roles. Add ownership links ("A owns 30% of Holding ApS", "Holding ApS owns 100% of the client"). The app multiplies down each chain and sums all paths to get each person's effective share, and marks persons with 25% or more as UBO.
   - **Sanctions screening**: set the threshold, click **Screen all names**. For each candidate the app shows the score, listed name or alias, list id, birth date and countries. Mark each one *open*, *ruled out* or *confirmed* and write the evidence in the note.
   - **Risk & decision**: choose the risk factors. The app calculates points, a risk level and a suggested decision, and shows which policy rules were triggered. Then record your own decision, reason and evidence.
   - **Questions & notes**: log each Client Contact Desk question, why you asked it, the answer and what it changed. The counter in the top bar shows how many of the 15 questions are used.
4. **Export CSV** downloads four files: `kyc_clients.csv`, `kyc_owners_management.csv`, `kyc_screening.csv`, `kyc_questions.csv`.
5. **Backup / Restore** saves and reloads all data as one JSON file (use it before you change browser or computer).

## Scoring rule (plain words)

Every client starts at 0 points. Points are added for each risk factor:

- Cash: some cash +1, cash-heavy business +3.
- Countries: links outside the EU +1, a high-risk or sanctioned country +3.
- Sector: a high-risk sector (e.g. cash trade, crypto, real estate, gambling, car trade) +2.
- PEP: the client, an owner or a director is a PEP, a family member or a close associate +3.
- Source of funds: only partly documented +1, unclear or not documented +3.
- Inconsistencies between documents or answers: minor and explained +1, major or unexplained +3.
- Complex ownership: two or more company layers between the client and the people behind it +1 (calculated automatically).

**0–1 points = Low** (accept). **2–3 points = Normal** (accept). **4 or more = High** (accept with enhanced due diligence, or escalate).

The points never override the firm policy. These rules come first:

- A confirmed sanctions match on the client, on anyone owning 25% or more (directly or indirectly), or on anyone in management → **Reject**.
- A confirmed listed owner below 25% → **High**, escalate to the compliance partner.
- Beneficial owners not identified → due diligence not complete → **not accepted**.
- Any PEP link → at least **High** with enhanced due diligence, never a rejection on its own.
- A screening candidate still **open** blocks acceptance. A candidate is only ruled out or confirmed with evidence (birth date, nationality, ID), never on the name alone.

## Name matching

Names are lower-cased; Æ/Ø/Å become ae/oe/aa and accents are removed. The score (0–1) is the higher of two Jaro–Winkler measures: one on the whole name with the words sorted (so "Petrov, Ivan" matches "Ivan Petrov"), and one word by word. Every listed name and alias is compared, and the best one is shown.

## Put it on GitHub Pages

1. On github.com, create a new **public** repository, e.g. `kyc-app`.
2. Click **Add file → Upload files**, upload `index.html` and `README.md`, and commit.
3. Go to **Settings → Pages**, under *Source* choose **Deploy from a branch**, branch `main`, folder `/ (root)`, and save.
4. After a minute the app is live at `https://<your-username>.github.io/kyc-app/`.

Do not upload `targets.simple.csv` or your client data to the repository; the app loads the list from your own computer.
