# CI/CD-flöde för Flask-app

## 1. CI/CD-flöde för ett tänkt projekt: Flask-app

### a. Vad händer när utvecklaren pushar kod?
- När kod pushas till GitHub triggas en CI/CD-pipeline automatiskt (t.ex. via GitHub Actions).
- Pipeline startar med att:
  - Klona repot.
  - Installera beroenden (t.ex. `pip install -r requirements.txt`).
  - Köra tester.
  - Om testerna lyckas, bygga och (ev.) deploya applikationen till t.ex. en server eller molntjänst som Heroku, AWS eller Azure.

---

### b. Vilka tester ska köras?
- Enhetstester (t.ex. pytest för Flask-routes och funktioner).
- Integrationstester (kontrollera att API:er fungerar ihop).
- Linting och kodstil (t.ex. flake8, black för Python).
- (Eventuellt) Sårbarhetsskanning av beroenden (t.ex. med pip-audit).

---

### c. När och hur byggs applikationen?
- **När:** Efter att koden har blivit verifierad av testerna.
- **Hur:**
  - En Docker-bild kan byggas med en Dockerfile, eller
  - Paketera koden för deployment (om det är en enklare deployment utan Docker).

---

### d. Flödet (enkel lista):Utvecklare pushar kod → 
- GitHub Actions triggas → 
- Installera beroenden → 
- Kör kodkvalitetstester (linting) → 
- Kör enhetstester → 
- Bygg applikationen (Docker eller liknande) → 
- Deploya till produktion/testmiljö
---

### e. Vad skulle kunna gå fel i flödet?
- Tester misslyckas → blockerar deployment.
- Beroenden kan saknas eller ha fel versioner → build misslyckas.
- Fel i konfiguration (miljövariabler, API-nycklar saknas).
- Problem med deploy (t.ex. servern tar inte emot nya versionen).
- Tidsouts (pipeline tar för lång tid).

---

### f. Vilka tester är viktigast i just ditt flöde?
- Enhetstester på kritiska funktioner (t.ex. API endpoints).
- Integrationstester (att Flask-servern verkligen kan prata med databasen).
- Linting för kodstandard (hitta problem tidigt).

---

### g. Hur kan testning förbättra kvaliteten?
- Upptäcker buggar tidigt innan kod når produktion.
- Säkerställer att ny kod inte förstör befintlig funktionalitet (regressionstester).
- Ökar förtroendet för deployment – trygghet att förändringar är stabila.
- Leder till bättre kodstruktur eftersom kod som är lätt att testa ofta är bättre designad.

---

## 2. Hur tror du att automatiserad testning påverkar en utvecklares vardag?
- Ger snabb feedback direkt efter en push – mindre oro för "smygbuggar".
- Sparar tid på manuella tester.
- Gör det enklare att våga göra ändringar och refaktorisering.
- Kan ibland vara frustrerande om tester är långsamma eller instabila.

---

## 3. Fördel och nackdel med testning inbyggd i CI/CD-flödet

**Fördelar:**
- Säkerställer att bara testad och verifierad kod går till produktion.
- Ökar hela teamets kvalitet och leveranshastighet.

**Nackdelar:**
- Om pipelines är långsamma kan det göra kodleveranser frustrerande sega.
- Krav på bra underhåll av tester (trasiga tester blockerar allt arbete).

---

## 4. Om du skulle införa CI/CD i ett skarpt projekt – vad skulle du börja med?
- Börja med att införa enkel automatiserad testkörning på varje push (CI).
- Sätta upp linting och kodkvalitet-kontroller.
- När tester är stabila – bygga vidare med automatisk bygg och automatisk deploy (CD).

---

## 5. Hur skiljer sig GitHub Actions från andra CI/CD-verktyg?

**GitHub Actions:**
- Direkt integrerat i GitHub, ingen extern server behövs.
- Konfiguration via YAML-filer (.github/workflows/).
- Gratis/minimalt pris för små projekt.

**Skillnader mot andra:**
- **Jenkins** kräver egen server och mer installation.
- **GitLab CI** liknar Actions men är kopplat till GitLab, inte GitHub.
- **CircleCI** är enklare på vissa sätt men kräver separat konto och integration.

---

## Exempel på GitHub Actions Workflow (.github/workflows/ci.yml)

```yaml
name: CI for Flask App

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
    - name: Klona repot
      uses: actions/checkout@v4

    - name: Sätt upp Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.11'

    - name: Installera beroenden
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Lint-kontroll (flake8)
      run: |
        pip install flake8
        flake8 .

    - name: Kör tester (pytest)
      run: |
        pip install pytest
        pytest tests/

    - name: Bygg Docker-bild (valfritt steg)
      if: success()
      run: |
        docker build -t flask-app:latest .

    - name: Deploy (exempel - pseudo)
      if: success()
      run: echo "Här skulle deployment ske, t.ex. till Heroku eller AWS"

