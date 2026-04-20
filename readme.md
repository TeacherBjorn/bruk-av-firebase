# Firebase Hosting med GitHub Actions

En kortfattet guide for å deploye et HTML/CSS/JS-prosjekt til Firebase Hosting automatisk via GitHub.

---

## Forutsetninger

- Et GitHub-repo med prosjektet ditt
- En Google-konto
- Node.js installert lokalt

---

## Steg 1 – Sett opp Firebase-prosjekt

1. Gå til [console.firebase.google.com](https://console.firebase.google.com)
2. Klikk **Add project** og følg stegene
3. Du trenger ikke Google Analytics

---

## Steg 2 – Installer Firebase CLI

```bash
npm install -g firebase-tools
firebase login
```

---

## Steg 3 – Initialiser Firebase i prosjektet

Kjør dette i rotmappen til prosjektet ditt:

```bash
firebase init hosting
```

Svar på spørsmålene slik:

| Spørsmål | Svar |
|---|---|
| Which project? | Velg prosjektet du lagde |
| Public directory? | `.` (eller f.eks. `public`) |
| Single-page app? | `No` (med mindre du har en SPA) |
| Overwrite index.html? | `No` |

Dette lager `firebase.json` og `.firebaserc` i prosjektmappen.

---

## Steg 4 – Generer en CI-token

```bash
firebase login:ci
```

Kopier token-en du får — den skal inn i GitHub.

---

## Steg 5 – Legg token i GitHub Secrets

1. Gå til repoet ditt på GitHub
2. **Settings → Secrets and variables → Actions → New repository secret**
3. Navn: `FIREBASE_TOKEN`
4. Verdi: token-en fra forrige steg

---

## Steg 6 – Lag en GitHub Actions-workflow

Opprett filen `.github/workflows/deploy.yml` i repoet ditt:

```yaml
name: Deploy til Firebase

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Deploy til Firebase Hosting
        uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: ${{ secrets.GITHUB_TOKEN }}
          firebaseServiceAccount: ${{ secrets.FIREBASE_TOKEN }}
          channelId: live
          projectId: ditt-prosjekt-id   # <-- bytt ut med ditt Firebase-prosjekt-ID
```

> Finn prosjekt-ID-en i Firebase Console under prosjektinnstillingene.

---

## Steg 7 – Push og deploy

```bash
git add .
git commit -m "Legg til Firebase-konfig og deploy-workflow"
git push
```

GitHub Actions kjører nå automatisk og deployer til Firebase Hosting hver gang du pusher til `main`.

---

## Nyttige kommandoer

```bash
firebase deploy          # manuell deploy fra terminalen
firebase hosting:channel:deploy preview   # deploy til preview-URL uten å publisere
firebase open hosting:site   # åpne siden i nettleseren
```

---

## Spark vs. Blaze 

Spark er gratis og passer godt til små prosjekter. Blaze benyttes når prosjektet skaleres (flere brukere) og det blir behov for dynamiske elementer (bruk av vue, react o.l)., og tilgang til andre google funksjoner. Til vårt bruk, og i denne oppgaven, benyttes **Spark**. 





## Filstruktur etter oppsett

```
prosjektet/
├── .github/
│   └── workflows/
│       └── deploy.yml
├── index.html
├── style.css
├── script.js
├── .firebaserc
└── firebase.json
```