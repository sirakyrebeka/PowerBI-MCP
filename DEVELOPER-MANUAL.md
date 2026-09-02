# PowerBI-MCP Projekt - Fejlesztő Kézikönyv

**Verzió:** 1.0  
**Létrehozva:** 2026-09-01  
**Projekt:** PowerBI-MCP (HighValueClaims)  
**Curent Branch:** 09-pbip-implementation

---

## Tartalomjegyzék

1. [Projekt Áttekintés](#projekt-áttekintés)
2. [Fejlesztői Környezet Beállítása](#fejlesztői-környezet-beállítása)
3. [Projekt Szerkezete](#projekt-szerkezete)
4. [Munkamenet Kezelés](#munkamenet-kezelés)
5. [Branchek és Funkciók](#branchek-és-funkciók)
6. [Aktuális Fejlesztési Feladatok](#aktuális-fejlesztési-feladatok)
7. [Power BI PBIP Fejlesztés](#power-bi-pbip-fejlesztés)
8. [Gyakori Problémák és Megoldások](#gyakori-problémák-és-megoldások)
9. [Hasznos Parancsok](#hasznos-parancsok)

---

## Projekt Áttekintés

### Mi a projekt?

**PowerBI-MCP** egy Power BI fejlesztési projekt, amely a **HighValueClaims** (Nagyértékű Károk) elemzési alkalmazás megvalósítására épül.

### Cél

A projekt célja egy Power BI megoldás létrehozása, amely az insurance károk régió és termék szerinti elemzéseit végzi, különös tekintettel az 500.000 Ft feletti **nagyértékű károkra**.

### Use Case (UC-004)

**Business Objective:**  
Szeretném látni a nagyértékű károkat régió és termék szerint.

**Definíció:**  
Nagyértékű kár = > 500.000 Ft kárösszeg

**Dimenziók:**
- Régió (Budapest, Nyugat, Dél, Kelet, Észak)
- Termék (Casco, Lakás, Élet, Utas, Vállalati)

**Mérőszámok:**
- Nagyértékű kárösszeg (összeg az 500k Ft feletti tételekre)
- Nagyértékű kár darabszáma

### Projekt Technológiája

| Komponens | Technológia |
|-----------|-------------|
| **BI Platform** | Power BI (PBIP format - Project for Power BI) |
| **Adatmodell** | Tabular Model (TMDL - Tabular Model Definition Language) |
| **Kalkuláció** | DAX (Data Analysis Expressions) |
| **Verziókezelés** | Git / GitHub |
| **Dokumentáció** | Markdown |
| **Adatforrás** | CSV (Claims.csv) |

---

## Fejlesztői Környezet Beállítása

### Előfeltételek

- **Power BI Desktop** (legújabb verzió)
  - Letöltés: https://powerbi.microsoft.com/en-us/desktop/
  - PBIP fejlesztéshez szükséges featureTogglét engedélyezni kell
  
- **Git** (v2.30+)
  - Mac: `brew install git`
  - Windows: https://git-scm.com/download/win

- **VS Code** vagy hasonló szövegszerkesztő
  - Opcionális markdown preview extension-ökhöz

- **Python 3.8+** (opcionális, tesziadatok generálásához)

### Git Klónozás

```bash
# Repository klónozása
git clone https://github.com/reeistvan/PowerBI-MCP.git
cd PowerBI-MCP

# Projekt branch kiválasztása (09-pbip-implementation)
git switch 09-pbip-implementation
git pull
```

### PBIP Projekt Megnyitása Power BI-ban

1. **Power BI Desktop megnyitása**
2. **File → Open** → Navigálás ide: `/pbip/HighValueClaims.pbip`
3. **Semantic Model Editor** és **Report Editor** automatikusan betöltődik

### Adatok Betöltése (Első Indítás)

Az adatok már benne vannak a `data/Claims.csv` fájlban. Az adatmodellben az alábbi táblák már definiálva vannak:

- **FactClaims** - Kár tételek (ClaimID, ClaimAmount, ClaimDate, stb.)
- **DimRegion** - Régiók (RegionName)
- **DimProduct** - Termékek (ProductName)

---

## Projekt Szerkezete

```
PowerBI-MCP/
├── .git/                          # Git repository meta
├── docs/                          # Dokumentáció
│   ├── README.md                  # Dokumentáció index
│   ├── pbip-development-guide.md  # PBIP fejlesztési útmutató
│   ├── glossary.md                # Üzleti szótár
│   ├── data-model-design-004.md   # Adatmodell tervezet
│   ├── measure-design-004.md      # Mérőszám specifikációk
│   ├── report-design-004.md       # Riport terv
│   └── ... (további dokumentációk)
│
├── pbip/                          # Power BI projekt (PBIP format)
│   ├── HighValueClaims.pbip       # PBIP projekt manifest
│   ├── HighValueClaims.SemanticModel/
│   │   ├── definition/
│   │   │   ├── model.tmdl         # Adatmodell definíció
│   │   │   ├── tables/            # Tábladefiníciók (TMDL)
│   │   │   ├── relationships.tmdl # Kapcsolatok
│   │   │   └── database.tmdl      # Adatbázis beállítások
│   │   └── .pbi/
│   │       └── editorSettings.json # Editor beállítások
│   │
│   ├── HighValueClaims.Report/
│   │   ├── definition/
│   │   │   ├── report.json        # Riport meta
│   │   │   ├── pages/             # Riport oldalak
│   │   │   │   ├── f193488021e74c1928ce/  # Executive Summary
│   │   │   │   ├── pages.json     # Oldal lista
│   │   │   └── version.json       # Verzió info
│   │   └── .platform/
│   │
│   └── _backups/                  # Biztonsági másolatok
│
├── skills/                        # MCP Skills (AI Agent-ek)
│   ├── DataModel-Designer.md      # Adatmodell tervezési skill
│   ├── DAX-Generator.md           # DAX kódgeneráló skill
│   ├── Report-Designer.md         # Riport tervezési skill
│   ├── Task-Orchestrator.md       # Feladat koordinátor skill
│   ├── Glossary-Builder.md        # Szótár builder skill
│   └── UC-Normalizer.md           # Use case normalizáló skill
│
├── tasks/                         # Fejlesztési feladatok
│   ├── README.md                  # Feladat dokumentáció
│   ├── TASK-004-01.md            # Adatanalízis és szótár
│   ├── TASK-004-02.md            # Adatmodell tervezés
│   ├── TASK-004-03.md            # Measure specifikáció
│   ├── TASK-004-04.md            # Riport tervezés
│   └── TASK-004-05.md            # Implementáció és teszt
│
├── use_cases/                     # Üzleti use case-ek
│   ├── README.md                  # Use case dokumentáció
│   └── UC-004.md                  # HighValueClaims use case
│
├── dax/                           # DAX specifikációk
│   └── DAX-004.md                 # HighValueClaims DAX measures
│
├── data/                          # Tesztadatok
│   ├── Claims.csv                 # Kár tételek (adatforrás)
│   ├── generate_claims.py         # CSV generáló Python script
│   └── mock-semantic-model.md     # Mock Power BI modell
│
├── governance/                    # Governance dokumentáció
│   └── review-checklist.md        # Code/Model review checklist
│
├── start-work.sh                  # Napi munka indító script
├── save-work.sh                   # Munka mentő script
├── GIT-HELP.md                    # Git cheat sheet (magyar)
└── README.md                      # Projekt főoldala (még nincs)
```

### Fájlok Magyarázata

| Mappa | Tartalom |
|-------|----------|
| **docs/** | Teljes dokumentáció: üzleti logika, adatmodell, mérőszámok, riportok |
| **pbip/** | Power BI projekt (verziókezelésre alkalmas PBIP format) |
| **skills/** | AI Agent instrukciók (Copilot MCP skillekhez) |
| **tasks/** | Fejlesztési feladatok, specifikációk, nyitott kérdések |
| **use_cases/** | Üzleti követelmények, user stories |
| **dax/** | DAX mérőszám specifikációk és példakódok |
| **data/** | Teszteadatok (CSV) és mock adatmodell |
| **governance/** | Review checklist-ek és governance dokumentáció |

---

## Munkamenet Kezelés

### Napi Munka Indítása

**Automatikus indítás (ajánlott):**

```bash
cd /Users/reeistvanandras/GITHUB_reeistvan/PowerBI-MCP
./start-work.sh
```

Ez a script:
1. ✅ Ellenőrzi, hogy Git repositoryban vagy-e
2. ✅ Megakadályozza branchváltást mentetlen munkával
3. ✅ Letölti a legújabb változásokat (`git fetch`)
4. ✅ Átváltson az `09-pbip-implementation` branchre
5. ✅ Frissít a legújabb remote verziót (`git pull --ff-only`)
6. ✅ Megnyitja VS Code-ot

**Manuális indítás (ha szükséges):**

```bash
git switch 09-pbip-implementation
git pull
code .
```

### Munka Mentése

**Gyors mentés script (ajánlott):**

```bash
./save-work.sh "Claims adatmodell kész"
```

**Manuális mentés:**

```bash
# 1. Ellenőrzi a módosítások
git status

# 2. Összes módosítás hozzáadása
git add .

# 3. Commit létrehozása
git commit -m "Claims adatmodell kész"

# 4. Feltöltés GitHubra
git push
```

### Nap Vége

```bash
./save-work.sh "End of day"
git push
```

Másik gépen/fejlesztőn:

```bash
git pull
# A munka folytatható onnan, ahol előző fejlesztő abbahagyta
```

---

## Branchek és Funkciók

### Branch Struktúra

```
main (stabil)
├── 09-pbip-implementation (aktív fejlesztés)
├── 10-mcp-skills (AI Agent skills)
└── 11-devops-automation (CI/CD, scriptek)
```

### Branch Leírások

| Branch | Felelős | Tartalom |
|--------|---------|----------|
| **main** | Stabil release | Terméklépés kész, tesztelt kód |
| **09-pbip-implementation** | PBIP fejlesztés | Power BI projekt, TMDL, DAX, riportok |
| **10-mcp-skills** | AI Agent fejlesztés | Copilot MCP skillekk, promptok, agentek |
| **11-devops-automation** | DevOps | Shell scriptek, GitHub Workflows, automatizáció |

### Branch Váltás

```bash
# Melyik branch vagyok most?
git branch --show-current

# Átváltás másik branchre
git switch main
git switch 09-pbip-implementation
git switch 10-mcp-skills

# Új branch létrehozása
git switch -c 12-training-pack
git push -u origin 12-training-pack
```

### Merge (Branch összefésülés)

```bash
# Main frissítés hozzáadása feature branchhez
git switch 09-pbip-implementation
git merge main

# Ha konfliktus van:
# 1. VS Code-ban megjavítjuk a conflictokat
# 2. Folytatjuk a mergét
git commit --no-edit
git push
```

---

## Aktuális Fejlesztési Feladatok

### TASK-004 Sorozat (HighValueClaims)

A projekt 5 feladatból áll:

#### TASK-004-01: Adatanalízis és Szótár
**Status:** ✅ Dokumentálva  
**Cél:** UC-004-hez szükséges adatdimenziók és mérőszámok azonosítása  
**Inputok:**
- use_cases/UC-004.md (üzleti követelményt)
- docs/glossary.md (üzleti szótár)

**Deliverables:**
- Szükséges adatelemek listája
- Kérdések a hiányzó adatokkal kapcsolatban
- Adatellenőrzési szempontok

**Dokumentáció:** `tasks/TASK-004-01.md`

---

#### TASK-004-02: Adatmodell Tervezés
**Status:** 🔄 Fejlesztés alatt (PBIP stage)  
**Cél:** Star schema adatmodell tervezése  
**Inputok:**
- TASK-004-01 output
- docs/glossary.md
- dax/DAX-004.md

**Deliverables:**
- Fact tables (FactClaims)
- Dimension tables (DimRegion, DimProduct, DimDate)
- Relationships
- Grain (részletezettség)
- Business Mapping

**Dokumentáció:** `docs/data-model-design-004.md`

**PBIP implementáció:**
```
pbip/HighValueClaims.SemanticModel/
  definition/
    tables/
      Claims.tmdl
      DimRegion.tmdl
      DimProduct.tmdl
    relationships.tmdl
```

---

#### TASK-004-03: Mérőszám Specifikáció
**Status:** 📋 Dokumentálva, DAX kódok készen  
**Cél:** Measure-ek definiálása és DAX kód  
**Inputok:**
- TASK-004-02 (adatmodell)
- dax/DAX-004.md

**Szükséges Measure-ek:**
1. **Nagyértékű kárösszeg**
   ```dax
   Nagyértékű kárösszeg :=
   CALCULATE(
       SUM(FactClaims[ClaimAmount]),
       FactClaims[ClaimAmount] > 500000
   )
   ```

2. **Nagyértékű kár darabszáma**
   ```dax
   Nagyértékű kár darabszáma :=
   CALCULATE(
       COUNTROWS(FactClaims),
       FactClaims[ClaimAmount] > 500000
   )
   ```

**Dokumentáció:** `dax/DAX-004.md`

---

#### TASK-004-04: Riport Tervezés
**Status:** 📋 Terv elkészült  
**Cél:** Power BI riport megjelenés és interakciók  

**Oldalak:**
1. **Executive Summary**
   - KPI cardok: Nagyértékű kárösszeg, Darabszám
   - Havi trend
   - Régió szerinti TOP 5

2. **Region Analysis**
   - Mátrix: Régió × Termék
   - Nagyértékű kárösszeg szegmentálása

3. **Product Analysis**
   - Termék × Régió szegmentálás
   - Trend vizualizáció

**Dokumentáció:** `docs/report-design-004.md`

---

#### TASK-004-05: Implementáció és Teszt
**Status:** 🚀 Aktív fejlesztés  
**Cél:** PBIP projekt véglegesítése, teszt, publikálás  

**Lépések:**
1. ✅ Semantic model létrehozása (TMDL)
2. ✅ Adatok betöltése (CSV Import)
3. ✅ Kapcsolatok (relationships)
4. ✅ Measure-ek (DAX)
5. 🔄 Riport oldalak (Report Designer)
6. ⏳ Teszt és validáció
7. ⏳ Publikálás (Power BI Service)

---

## Power BI PBIP Fejlesztés

### Mik a PBIP fájlok?

**PBIP = Project for Power BI**

Egy Git-barát format, amely helyett a bináris `.pbix` fájloknak Text-based TMDL fájlokat használ.

**Előnyei:**
- ✅ Verziókezelésre alkalmas (Git)
- ✅ Merge-elhető (konfliktuskezeléshez)
- ✅ Diff-elhető (review-hoz)
- ✅ Team fejlesztésre optimális

### PBIP Projekt Szerkezete

```
HighValueClaims.pbip
├── HighValueClaims.pbip                    # Projekt manifest
├── HighValueClaims.SemanticModel/
│   ├── definition.pbism                   # Semantic Model manifest
│   ├── definition/
│   │   ├── model.tmdl                     # Model definíció
│   │   ├── database.tmdl                  # Adatbázis beállítások
│   │   ├── cultures/
│   │   │   └── en-US.tmdl                 # Lokalizáció
│   │   ├── tables/
│   │   │   ├── Claims.tmdl                # FactClaims tábla
│   │   │   ├── DimRegion.tmdl             # DimRegion tábla
│   │   │   ├── DimProduct.tmdl            # DimProduct tábla
│   │   │   └── ... (további táblák)
│   │   └── relationships.tmdl             # Relációk
│   └── .pbi/
│       └── editorSettings.json            # Editor beállítások
│
└── HighValueClaims.Report/
    ├── definition.pbir                    # Report manifest
    ├── definition/
    │   ├── report.json                    # Report meta
    │   ├── pages/
    │   │   ├── pages.json                 # Oldal lista
    │   │   ├── f193488021e74c1928ce/      # Page ID (Executive Summary)
    │   │   │   └── page.json              # Page definíció
    │   │   └── ... (további oldalak)
    │   └── version.json                   # Verzió
    └── .platform/
```

### TMDL Fájlok Szerkesztése

**TMDL = Tabular Model Definition Language**

A `definition/tables/` mappa YAML-szerű szintaxisban definiálja az objektumokat.

**Claims.tmdl Példa:**

```tmdl
table Claims
	lineageTag: 12345678-1234-1234-1234-123456789012
	sourceLineageTag: <default>

	column ClaimID
		dataType: string
		lineageTag: aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
		sourceLineageTag: ClaimID

	column ClaimAmount
		dataType: decimal
		lineageTag: ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj
		sourceLineageTag: ClaimAmount

	column RegionName
		dataType: string
		lineageTag: kkkkkkkk-llll-mmmm-nnnn-oooooooooooo
		sourceLineageTag: RegionName

	measure 'Nagyértékű kárösszeg'
		lineageTag: pppppppp-qqqq-rrrr-ssss-tttttttttttt

		expression = CALCULATE(SUM('Claims'[ClaimAmount]), 'Claims'[ClaimAmount] > 500000)
```

### Power BI Desktop PBIP Szerkesztése

**Semantic Model szerkesztésé:**

1. Power BI Desktop megnyitása
2. `HighValueClaims.pbip` fájl megnyitása
3. **View → Semantic Model** → TMDL módban szerkeszthetõ
4. Vagy **Model** view a visual editor-ban

**Report szerkesztésé:**

1. **View → Report** (vagy Report Editor)
2. Oldalak szerkesztése drag-drop módban
3. Vizualizációk hozzáadása

**Mentés:**

```bash
# Power BI-ban: Ctrl+S vagy File → Save
# A módosítások automatikusan a TMDL/JSON fájlokba kerülnek
```

---

## Gyakori Problémák és Megoldások

### Git Problémák

#### Problem: "cannot switch branch while merging"

```bash
# Ellenőrzés
git status

# Megoldás (opció 1: merge befejezése)
git commit --no-edit
git push

# Megoldás (opció 2: merge visszavonása)
git merge --abort
```

#### Problem: "You have unstaged changes"

```bash
# Opció 1: Mentés
git add .
git commit -m "Aktuális munka"

# Opció 2: Stash (ideiglenes mentés)
git stash
# Később újra hozás:
git stash pop
```

#### Problem: Brancheket nem látom GitHubon

```bash
git push -u origin <branch-name>
```

#### Problem: Main nem szinkronizált

```bash
git switch main
git fetch origin
git merge origin/main
git push
```

---

### Power BI / PBIP Problémák

#### Problem: TMDL file nem nyílik meg

- Győződj meg, hogy a szyntaxisban nincs hiba
- Használd VS Code-ot TMDL szerkesztéshez
- JSON formatum-t is ellenőrizd

#### Problem: Measure nem működik

- Ellenőrizd a tábla és oszlop nevét pontosan
- Győződj meg, hogy tábla a Semantic Model-ben már betöltött
- DAX szintaxis ellenőrzés: `dax/DAX-004.md`

#### Problem: Riport oldal nem jelenik meg

- Ellenőrizd `pages.json` fájlban az oldal registrációt
- Töröld a page-t és add hozzá újra a Report editorban

---

### Adatmodell Problémák

#### Problem: Relationship nem működik

1. Ellenőrizd az oszlopneveket pontosan
2. Ellenőrizd az oszlop típusait (string, int, decimal)
3. Ellenőrizd a cardinalityt (1:*, 1:1)
4. Biztosítsd, hogy az oszlopok nem NULL értékeket tartalmaznak

---

## Hasznos Parancsok

### Git Parancsok

```bash
# Aktuális branch megjelenítése
git branch --show-current

# Összes branch megjelenítése
git branch -a

# Status ellenőrzés
git status

# Legújabb commitok
git log --oneline -10

# Commit részletei
git show <commit-hash>

# Módosítások előnézete
git diff

# Napi munka indítása
./start-work.sh

# Munka mentése
./save-work.sh "Üzenet"
```

### Markdown Dokumentáció

```bash
# VS Code markdown preview
# Ctrl+Shift+V a szerkesztőben

# Vagy command line:
# brew install marked (Mac)
marked docs/pbip-development-guide.md
```

### Python Tesztadatok Generálása

```bash
cd data/
python3 generate_claims.py > Claims_new.csv
# Fájl kézi kiegészítésé után:
git add Claims.csv
git commit -m "Tesztadatok frissítése"
```

---

## Dokumentáció Hivatkozások

### Üzleti Dokumentáció

- **Use Case:** `use_cases/UC-004.md` - Üzleti követelmények
- **Szótár:** `docs/glossary.md` - Üzleti fogalmak
- **Adatmodell Terv:** `docs/data-model-design-004.md`
- **Mérőszám Terv:** `dax/DAX-004.md`
- **Riport Terv:** `docs/report-design-004.md`

### Fejlesztési Dokumentáció

- **PBIP Guide:** `docs/pbip-development-guide.md`
- **Feladat Lista:** `tasks/README.md`
- **Git Segítség:** `GIT-HELP.md`

### AI Agent Skills

- **DataModel-Designer:** `skills/DataModel-Designer.md`
- **DAX-Generator:** `skills/DAX-Generator.md`
- **Report-Designer:** `skills/Report-Designer.md`
- **Task-Orchestrator:** `skills/Task-Orchestrator.md`

---

## Kapcsolatfelvétel és Támogatás

### Kérdések és Problémák

1. **Dokumentáció:** Első lépés mindig az `docs/` mappa
2. **Use Case:** `use_cases/UC-004.md`
3. **Feladatok:** `tasks/README.md` - Nyitott kérdések
4. **GitHub Issues:** Repository issues tab

### Közreműködés

1. **Branch:** A feature-ét egy saját branchen dolgozz
2. **Commit:** Gyakori, leíró üzenetekkel
3. **Push:** Napi végén
4. **PR:** Feature teljesítésénél (ha szükséges)

---

## Projekt Status

**Utolsó frissítés:** 2026-09-01

| Komponens | Status | Progress |
|-----------|--------|----------|
| Use Case (UC-004) | ✅ Complete | 100% |
| Feladat specifikáció | ✅ Complete | 100% |
| Adatmodell tervezet | ✅ Complete | 100% |
| DAX mérőszámok | ✅ Complete | 100% |
| PBIP Semantic Model | 🔄 In Progress | 80% |
| PBIP Report Design | 🔄 In Progress | 50% |
| Teszt és validáció | ⏳ Pending | 0% |
| Publikálás | ⏳ Pending | 0% |

---

## Hasznos Linkek

- Power BI Dokumentáció: https://docs.microsoft.com/en-us/power-bi/
- DAX Reference: https://dax.guide/
- PBIP Format: https://learn.microsoft.com/en-us/power-bi/developer/projects/projects-overview
- Git Dokumentáció: https://git-scm.com/doc

---

## Jogi és Egyéb Megjegyzések

Ez a kézikönyv az **reeistvan/PowerBI-MCP** projekt fejlesztésére készült.

**Verziókezelés:** GitHub  
**Licenc:** Projekt-specifikus  
**Készült:** 2026-09-01

---

**Vége a Fejlesztő Kézikönyvnek**

Jó fejlesztést! 🚀
