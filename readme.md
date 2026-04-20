# Publiser nettsiden din med GitHub Desktop og Firebase

Når du lagrer filer i VS Code, dukker endringene opp i GitHub Desktop. Du skriver en kort melding, trykker **Push** – og nettsiden din oppdateres automatisk.

---

## Du trenger

- [VS Code](https://code.visualstudio.com/)
- [GitHub Desktop](https://desktop.github.com/)
- En GitHub-konto
- En Google-konto

---

## Steg 1 – Opprett et repo i GitHub Desktop

1. Åpne GitHub Desktop
2. Klikk **File → New Repository**
3. Gi repoet et navn (f.eks. `min-nettside`)
4. Husk å huke av **Initialize this repository with a README**
5. Klikk **Create Repository**
6. Klikk **Publish repository** øverst – da legges repoet ut på GitHub

---

## Steg 2 – Åpne prosjektet i VS Code

I GitHub Desktop: klikk **Open in Visual Studio Code**

Lag filene dine her:

```
min-nettside/
├── index.html
├── style.css
└── script.js
```

Hver gang du trykker **Ctrl+S** (lagre) i VS Code, vil endringen dukke opp i GitHub Desktop.

---

## Steg 3 – Sett opp Firebase

1. Gå til [console.firebase.google.com](https://console.firebase.google.com)
2. Klikk **Add project**, gi det et navn og følg stegene
3. Du trenger ikke Google Analytics eller bruk av Gemini

---

## Steg 4 – Koble Firebase til prosjektet ditt

Åpne terminalen i VS Code (**Terminal → New Terminal**) og kjør:

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
```

Svar på spørsmålene slik:

| Spørsmål | Svar |
|---|---|
| Which project? | Velg prosjektet du lagde |
| Public directory? | `.` |
| Single-page app? | `No` |
| Overwrite index.html? | `No` |

NB. Hvis produktet ditt ikke skal bygges av node.js må du spesifisere at du ikke trenger dette i veiviseren (du får et spørsmål om dette etter at du har lagt inn brukernavn/repo). 

To nye filer dukker opp i prosjektmappen: `firebase.json` og `.firebaserc`.

---

## Steg 5 – Hent en Service Account-nøkkel

1. Gå til Firebase Console → velg prosjektet ditt
2. Klikk tannhjulet → **Project settings → Service accounts**
3. Klikk **Generate new private key** → last ned JSON-filen
4. Åpne JSON-filen i VS Code og kopier innholdet
---

## Steg 6 – Legg nøkkelen inn i GitHub

1. Gå til repoet ditt på [github.com](https://github.com)
2. Klikk **Settings → Secrets and variables → Actions → New repository secret**
3. Navn: `FIREBASE_SERVICE_ACCOUNT`
4. Verdi: lim inn hele JSON-innholdet
5. Klikk **Add secret**

> Finn prosjekt-ID-en i Firebase Console under **Project settings → General**.

---

## Steg 7 – Lag en automatisk deploy-fil

Opprett mappen `.github/workflows/` i prosjektet ditt, og lag filen `deploy.yml` der inne med dette innholdet:

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
          firebaseServiceAccount: ${{ secrets.FIREBASE_SERVICE_ACCOUNT }}
          channelId: live
          projectId: ditt-prosjekt-id   # <-- bytt ut med ditt Firebase-prosjekt-ID

```

---

## Slik ser arbeidsflyten ut fremover

```
Lagre i VS Code (Ctrl+S)
        ↓
Endringer dukker opp i GitHub Desktop
        ↓
Skriv en kort beskjed (f.eks. "La til overskrift")
        ↓
Klikk "Commit to main"
        ↓
Klikk "Push origin"
        ↓
Nettsiden oppdateres automatisk på Firebase 🚀
```

Du kan se fremgangen under **Actions**-fanen i repoet ditt på GitHub. Når det står grønt hake er siden live.

---

## Filstruktur etter oppsett

```
min-nettside/
├── .github/
│   └── workflows/
│       └── deploy.yml
├── public/
│   └── index.html
│   └── style.css
│   └── script.js
├── .firebaserc
└── firebase.json
```

Legg merke til at alle filer ekspornert ut mot verden skal nå ligge under /public. 

---

> **OBS:** Ikke del eller commit JSON-filen med nøkkelen. Den skal kun brukes til å kopiere innholdet inn i GitHub Secrets.