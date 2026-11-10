# 🍊 Bahar — Orchard Care for Nagpur Mandarin

> **See the orchard · Decide · Act · Check it worked**

Bahar is an offline-first orchard app for Nagpur Mandarin growers. It uses an FAO-56 soil-water balance and a heat-stress index to score fruit-drop risk. It suggests actions and explains each one, runs safety checks before any watering, and afterwards checks whether the action worked. Photo and lab checks go to an agronomist. The app works in **English, Hindi and Marathi**.

The whole prototype is **one HTML file** (`Bahar.html`). It needs no install, no server and no build step.

![Bahar home dashboard](screenshots/02-home.png)

---

## Contents

- [Why Bahar](#why-bahar)
- [Key features](#key-features)
- [Screenshots](#screenshots)
- [How it works](#how-it-works)
- [User roles](#user-roles)
- [Getting started](#getting-started)
- [Demo walkthrough](#demo-walkthrough)
- [Configurable settings](#configurable-settings)
- [Data, privacy and security](#data-privacy-and-security)
- [Design principles](#design-principles)
- [Limitations](#limitations)
- [Roadmap](#roadmap)
- [Author](#author)

---

## Why Bahar

Nagpur Mandarin orchards lose much of their yield to **premature fruit drop**. The main causes are heat, dry air, uneven irrigation and pests or disease that go unnoticed. Most growers decide by eye, too late, and have no way to check whether an action helped.

Bahar turns sensor, weather and field observations into a **risk score for each zone**, an **ordered to-do list**, and a **closed loop** that checks whether each action made a difference.

---

## Key features

| Area | What it does |
|---|---|
| 🗺️ **Zone risk scores** | A 0–100 fruit-drop risk for each zone, with a category (Low / Moderate / High / Critical), an uncertainty band, a measure of how complete the data is, and a **"Why?"** breakdown of what is driving the score. |
| 💡 **Explainable actions** | Eight rule-based recommendations (R01–R08). Each one has a priority (24 h / 48 h / 7 days), the evidence behind it, and a defined "did it work?" check. |
| 💧 **Safe irrigation** | A FAO-56 water plan (mm, m³, hours). Six safety checks must pass before watering, and there is an emergency stop. Manual requests go through the same checks. |
| ✅ **Verification loop** | After watering, the app compares the expected and observed rise in soil moisture and marks the action **Worked / Did not work / Unclear**. |
| 📷 **Photo screening** | Leaf, fruit and tree photos get a visual indication (yellowing, spots, pest damage, blemish). Results with low confidence go to an agronomist. *Visual indication only, not a diagnosis.* |
| 🍊 **Fruit & Lab records** | Tagged and fallen fruit counts give a drop rate. Lab reports for soil, leaf and water are stored. **The app never gives a fertiliser or pesticide dose.** |
| 🌦️ **Weather and sensor simulation** | Nine reproducible situations and five sensor faults (stuck, drift, spike, dropout, offline). The same seed always gives the same data. |
| 👥 **Role-based access** | Farmer, Worker, Agronomist, Admin and Demonstrator, each with its own permissions. |
| 📊 **Reports and audit** | CSV exports, a full JSON backup and restore, a risk-history chart and an activity log. |
| 🌐 **Works anywhere** | Offline-first with sync on reconnect, three languages, light and dark mode, and a mobile layout with a bottom tab bar. |

---

## Screenshots

### Sign in
The sign-in screen offers one-tap demo accounts for every role.

![Sign in](screenshots/01-sign-in.png)

### Home
A risk gauge for each zone and the single most important task ("Do this first").

![Home](screenshots/02-home.png)

### Zones
Each zone card shows soil moisture, root-zone dryness, the stress index, fruit drop and photos waiting to be checked.

![Zones](screenshots/03-zones.png)

### Zone detail: "Why?"
A breakdown of the risk score by factor, plus the FAO-56 soil-water table (TAW, RAW, Dr, Ks, ET₀, ETc).

![Zone detail](screenshots/12-zone-detail.png)

### Actions
Recommendations ordered by priority, each with its evidence and source tags (SIM / PRED / USER), and Approve / Later / Reject buttons.

![Actions](screenshots/04-actions.png)

![Approve an action](screenshots/13-approve.png)

### Water
The water tank, a manual watering request, and the history of watering runs.

![Water](screenshots/05-water.png)

Once a watering action is approved, it shows up as **Ready to water** with all six safety checks listed:

![Ready to water with safety checks](screenshots/14-water-ready.png)

### Photos
Take or choose a photo, or try a sample. The app gives a visual label with a confidence level and passes uncertain results to an expert.

![Photos](screenshots/06-photos.png)

![Photo result](screenshots/15-photo-result.png)

### Fruit & Lab
Fruit-drop counts on tagged trees, and lab report entry. Values are stored exactly as typed and no dose is ever given.

![Fruit & Lab](screenshots/07-fruit-lab.png)

### Weather (simulation)
Pick a situation, a seed, the number of days and a sensor fault, then generate readings.

![Weather](screenshots/08-weather.png)

### Orchard
Orchard details, the list of zones, soil and irrigation setup, crop stage, and tree import from CSV.

![Orchard](screenshots/09-orchard.png)

### Review (agronomist)
The expert queue for uncertain photos and lab reports.

![Review](screenshots/10-review.png)

### Reports
Exports, the risk-history chart, versioned settings and the activity log.

![Reports](screenshots/11-reports.png)

### Hindi, dark mode and mobile

| Hindi | Dark mode |
|---|---|
| ![Hindi](screenshots/16-hindi.png) | ![Dark mode](screenshots/17-dark-mode.png) |

<p align="center"><img src="screenshots/18-mobile.png" width="320" alt="Mobile layout"></p>

---

## How it works

```
 Sensors / weather (simulated)  ──┐
 Fruit counts, photos, lab  ──────┼──►  Soil-water balance + stress index  ──►  Risk score
                                  │                                               │
                                  │                                               ▼
                                  │                                   Rules R01–R08 → Actions
                                  │                                               │
                                  │                               Approve / Later / Reject
                                  │                                               │
                                  │                                 Safety checks → Execute
                                  │                                               │
                                  └────────────────────────────  Verify: did it work? ◄─┘
```

### 1. Soil-water balance (FAO-56)

- **ET₀** (Hargreaves): `0.0023 · Ra · (Tmean + 17.8) · √(Tmax − Tmin)`
- **ETc** = ET₀ × Kc (Kc defaults to 0.65)
- **TAW** = 1000 × (FC − WP) × root depth, and **RAW** = p × TAW
- **Dr** is the root-zone depletion. **Ks** = 1 while Dr ≤ RAW, otherwise (TAW − Dr) / (TAW − RAW)

### 2. Environmental Stress Index (ESI)

```
ESI = 0.30·heat + 0.25·dry_air + 0.30·root_zone_depletion + 0.15·sensor_stress_term
```

- heat: Tmax scaled between 32 °C and 42 °C
- dry air: RHmin scaled between 50 % and 20 %

### 3. Fruit-drop risk score (0–100)

| Factor | Weight |
|---|---|
| Heat and dryness stress (ESI) | 0.30 |
| Recent fruit drop | 0.30 |
| Irregular watering | 0.15 |
| Photo signs | 0.15 |
| Crop-stage sensitivity | 0.10 |

**Safety guards** stop a real problem from being averaged away:
- Fruit drop ≥ 15 % → the score is at least **High** (50)
- Water stress (Ks < 0.5) in a sensitive crop stage → at least **Moderate** (25)
- Fruit drop ≥ 30 % together with ESI > 0.6 → **Critical** (75)

Every score also shows its uncertainty and how complete the data is.

### 4. Rules → actions

| Rule | Action | Check that it worked |
|---|---|---|
| R01 | Water zone | Soil moisture rises after watering |
| R02 | Too wet: pause water | Soil dries back to normal |
| R03 | Heat stress | Stress index falls |
| R04 | Expert: check photos | Expert decision recorded |
| R05 | Count fallen fruit | New count entered |
| R06 | Check sensor | Sensor readings back to OK |
| R07 | Expert: read lab report | Expert decision recorded |
| R08 | Wait, rain expected | — |

### 5. Safety checks before watering

1. Enough water in the tank
2. Duration within the zone limit
3. Volume within the limit (default ≤ 250 m³, ≤ 40 mm net)
4. No rain expected
5. Sensors agree
6. Emergency stop not active

If any check fails, the request is **blocked and logged**.

---

## User roles

| Permission | Farmer | Worker | Agronomist | Admin | Demo |
|---|:-:|:-:|:-:|:-:|:-:|
| Approve / defer / reject actions | ✅ | | | | ✅ |
| Expert decisions and review queue | | | ✅ | | ✅ |
| Start watering | ✅ | ✅ | | | ✅ |
| Manual watering, tank, release the emergency stop | ✅ | | | ✅* | ✅ |
| Upload photos, record observations | ✅ | ✅ | ✅ | | ✅ |
| Lab reports | ✅ | | ✅ | | ✅ |
| Edit orchard and zones | ✅ | | | ✅ | ✅ |
| Change settings (thresholds) | | | ✅ | ✅ | ✅ |
| Manage users and roles | | | | ✅ | |
| Export data | ✅ | | ✅ | ✅ | ✅ |
| Reset demo data | | | | ✅ | ✅ |

\*The admin can manage the tank and release the emergency stop, but cannot do manual watering.

New sign-ups have no role until the farmer or admin assigns one.

---

## Getting started

1. Download `Bahar.html`.
2. Open it in **Chrome or Edge**, on desktop or mobile.
3. Tap a demo user on the sign-in screen, or create an account.

That is all: no install and no server.

> Note: some embedded previews cannot keep saved data. To keep your sign-in and changes, open the downloaded file directly in a browser.

### Demo accounts

| User | Role |
|---|---|
| Ramesh | Farmer |
| Sunita | Worker |
| Dr. Deshmukh | Agronomist |
| Admin | Admin |
| Demo Guest | Demonstrator (can do almost everything) |

---

## Demo walkthrough

1. Sign in as **Demo Guest** and look at **Home**. Zone Z2 is High risk, and the top task is "Water zone Z2".
2. Open **Zones → Z2** and tap **Why?** to see the factors and the soil-water table.
3. Go to **Actions** and **Approve** the watering action.
4. Go to **Water**. The plan shows all six safety checks passing. Tap **Start watering**.
5. Back on **Actions**, the action shows *Done – checking*, then the result: **Worked**, **Did not work** or **Unclear**.
6. Go to **Weather**, choose **Rain coming** or **Sensor stuck**, and generate readings. The rules change: R08 asks you to wait, and R06 flags the bad sensor.
7. On **Photos**, try a sample photo. A low-confidence result goes to the agronomist's **Review** queue.
8. Switch to हिंदी or मराठी, try dark mode, and export a CSV from **Reports**.

---

## Configurable settings

Agronomists and admins can change these on the **Settings** screen. Every change creates a new **version** with a history record.

| Setting | Default | Meaning |
|---|---|---|
| `T_low` / `T_high` | 32 / 42 °C | Range over which heat stress is scaled |
| `RH_low` / `RH_crit` | 50 / 20 % | Range over which dry-air stress is scaled |
| `Kc` | 0.65 | Crop coefficient |
| `w_esi`, `w_drop`, `w_irr`, `w_img`, `w_stage` | 0.30, 0.30, 0.15, 0.15, 0.10 | Risk weights |
| `drop_guard` | 15 % | Fruit-drop rate that forces at least High risk |
| `conf_min` / `margin_min` | 0.70 / 0.20 | Minimum photo confidence and margin; below these the photo goes to an expert |
| `max_net` / `max_vol` | 40 mm / 250 m³ | Irrigation safety limits |
| `verify_ok` / `verify_low` | 0.70 / 0.40 | Response ratios for Worked / Unclear |

---

## Data, privacy and security

- All data stays **on the device**, in the browser's local storage. Nothing is sent to a server.
- Passwords are **salted and SHA-256 hashed**. Sessions expire after **8 hours**, and repeated wrong passwords trigger a **30-second lockout**.
- Every decision, run and settings change is recorded in the **activity log**.
- **Backup and restore** use a single JSON file.
- Every value is tagged with its source: **SIM** (simulated), **PRED** (calculated) or **USER** (entered by a person).

---

## Design principles

- **Advice the user can follow:** every score and action shows *why*.
- **People decide:** nothing is done without approval, and the farmer stays in control.
- **Safe by default:** safety checks, an emergency stop, limits, and a refusal to give chemical doses.
- **Closed loop:** every action has a measurable "did it work?" check.
- **Honest about uncertainty:** uncertainty bands, data completeness, and a disclaimer that photo results are not a diagnosis.
- **Built for the field:** works offline, in local languages, on a phone, with large touch targets.

---

## Limitations

- Sensor and weather data are **simulated**. No real hardware is connected yet.
- Photo screening is a **simple visual heuristic**, not a trained disease model.
- Data stays in one browser. Multi-device sync is simulated (online/offline queue).
- The risk weights and thresholds are starting values and need validation in the field with agronomists.

---

## Roadmap

- [ ] Connect real IoT soil-moisture and weather stations (MQTT / LoRaWAN)
- [ ] Use a live weather forecast API for rain and heat alerts
- [ ] Train a citrus leaf and fruit disease model on field images
- [ ] Add cloud sync and multi-orchard accounts
- [ ] Control irrigation valves through the safety checks
- [ ] SMS / WhatsApp alerts for High and Critical risk
- [ ] Field trials with Nagpur Mandarin growers to calibrate the weights

---

## Author

**Angshuman Bhagat**
GitHub: [@anxshh007](https://github.com/anxshh007) · Website: [anxshh007.github.io](https://anxshh007.github.io/) · LinkedIn: [angshuman-bhagat](https://www.linkedin.com/in/angshuman-bhagat-profilevisit/)

---

<p align="center"><i>Bahar (बहार): the flowering season that decides the year's crop.</i></p>
