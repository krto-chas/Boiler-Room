# Protokoll → Word (Boiler Room-protokoll)

Detta skript (`protokoll.py`) är en liten GUI-applikation (Tkinter) som hjälper dig att skapa och uppdatera ett **protokoll i Word-format (.docx)** baserat på ett formulär. Det finns även stöd för en GitHub-workflow (klona repo, välj branch, kopiera fil, commit & push) – men knappen för uppladdning är i nuvarande version **utkommenterad** i koden (se avsnittet “GitHub-uppladdning”).

## Funktioner

- **GUI-formulär** för:
  - Team
  - Datum (YYYY-MM-DD)
  - Deltagare (komma-separerat)
  - Vad vi jobbade med (en punkt per rad)
  - Hinder vi stötte på (en punkt per rad)
  - Status (🟢/🟡/🔴)
  - Nästa steg (en punkt per rad)
- Skapar Word-dokument i **`.docx`** med rubriker och punktlistor.
- **Settings-fil (`settings.json`)** i samma mapp som skriptet:
  - `output_dir` (var dokument sparas)
  - `last_team` (senast använda teamnamn)
  - `github.*` (konfiguration för repo/branch/commit)
- Vid start:
  - Beräknar planerat filnamn baserat på **(last_team + dagens datum)**.
  - Om filen finns: erbjuder att **ladda in** dokumentet i formuläret.
- Vid skapande:
  - Om filen redan finns: dialog med val **Skapa nytt protokoll** (ladda befintligt i formulär), **Uppdatera befintligt protokoll** (skriv över), eller **Avbryt**.
- Öppnar dokumentet automatiskt i standardprogram efter skapande (om möjligt).

## Förutsättningar

- Python 3.10+ (rekommenderat)
- Paketen:
  - `python-docx`
- För GitHub-flödet:
  - `git` installerat och åtkomst mot GitHub (SSH rekommenderas).

Installera beroende:

```bash
pip install python-docx
```

Kontrollera att git finns:

```bash
git --version
```

## Kom igång

1. Lägg `protokoll.py` i en valfri mapp.
2. Installera beroende:
   ```bash
   pip install python-docx
   ```
3. Starta programmet:
   ```bash
   python protokoll.py
   ```
4. Fyll i formuläret och klicka **Skapa Word-dokument**.

Dokumentet sparas i `output_dir` (standard: `./output`) och öppnas därefter.

## Filnamn och lagringsplats

Programmet skapar filnamn enligt:

```
protokoll_<Team>_<YYYY-MM-DD>.docx
```

Team “saneras” till ett säkert filnamn (mellanslag → `_`, specialtecken tas bort men ÅÄÖ stöds).

Exempel:

```
protokoll_Grupp_7_2026-01-22.docx
```

## settings.json

När programmet körs sparas inställningar i `settings.json` (i samma katalog som skriptet). Om filen saknas skapas den automatiskt vid avslut / efter skapande av dokument.

Exempel:

```json
{
  "output_dir": "./output",
  "last_team": "Grupp_7",
  "github": {
    "repo_url": "git@github.com:DITT_USER_ELLER_ORG/DITT_REPO.git",
    "local_repos_base": "~/dev",
    "repo_subdir": "docs/protokoll",
    "default_branch": "main",
    "commit_prefix": "Lägg till protokoll"
  }
}
```

### Fältbeskrivning

- `output_dir`: Sökväg där Word-filer sparas. Kan vara relativ (t.ex. `./output`) eller med `~`.
- `last_team`: Senast använda teamnamn (används vid uppstart för att hitta ev. befintlig fil).
- `github.repo_url`: Repo URL (SSH eller HTTPS).
- `github.local_repos_base`: Bas-mapp där repo klonas, t.ex. `~/dev`.
- `github.repo_subdir`: Underkatalog i repot där `.docx` kopieras in.
- `github.default_branch`: Default branch som föreslås i branch-dialogen.
- `github.commit_prefix`: Prefix i commit-meddelande.

## Uppdatera befintligt dokument

Om filen redan finns (samma Team + Datum):

- Välj **Skapa nytt protokoll** för att **ladda** befintlig fil in i formuläret (bra om du vill fortsätta redigera).
- Välj **Uppdatera befintligt protokoll** för att **skriva över** filen med nytt innehåll från formuläret.
- Välj **Avbryt** för att avbryta.

Notera att “Skapa nytt protokoll” i dialogen i praktiken betyder “ladda befintligt protokoll i formuläret och fortsätt därifrån”.

## GitHub-uppladdning (valfritt)

Koden innehåller en komplett GitHub-workflow:

- klona repo om det inte finns lokalt
- välja branch
- checkout (lokal/remote eller skapa ny baserat på origin/HEAD)
- kopiera `.docx` till `repo_subdir`
- `git add`, `commit`, `push`

### Viktigt: knappen är utkommenterad

I nuvarande kod är knappen för uppladdning utkommenterad:

```python
# ttk.Button(btns, text="Skicka till GitHub", command=on_upload_github).grid(...)
```

För att aktivera:
1. Avkommentera raden.
2. Spara och starta om programmet.

### Krav för att GitHub-flödet ska fungera

- `settings.json` måste ha `github.repo_url` ifyllt.
- Git måste kunna autentisera mot GitHub:
  - SSH-nycklar rekommenderas.
- Repot måste vara tillgängligt och du måste ha push-rättigheter.

## Felsökning

### “Datum måste vara i formatet YYYY-MM-DD”
Skriv datum exakt som `2026-01-22`.

### Dokumentet öppnas inte automatiskt
- Programmet försöker öppna med:
  - Windows: `os.startfile`
  - macOS: `open`
  - Linux: `xdg-open`
- Om inget händer: dokumentet är ändå skapat i `output_dir`. Öppna det manuellt via filhanteraren.

### Git-fel vid push/clone
- Kontrollera att `git` är installerat och att du kan köra `git clone`/`git push` i terminal.
- Kontrollera att `github.repo_url` är korrekt.
- Kontrollera SSH/HTTPS-auth (t.ex. `ssh -T git@github.com`).

### “Mappen finns redan men är inte ett git-repo”
Du har en existerande katalog med samma namn som repot i `github.local_repos_base`, men den innehåller inte `.git`.
- Rensa/byt namn på katalogen, eller
- ändra `github.local_repos_base` i `settings.json`.

## Licens

Ingen licens är angiven. Lägg till en `LICENSE` om du vill tydliggöra användningsvillkor.
