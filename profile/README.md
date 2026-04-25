# iSmartFrame

Benvenuto nell'organizzazione GitHub di **iSmartFrame**.
Questo documento è il riferimento operativo per tutti i membri del team.

---

## Indice

1. [Struttura dei repository](#struttura-dei-repository)
2. [Come lavorare — branch e PR](#come-lavorare)
3. [Software trasversale (shared-*)](#software-trasversale)
4. [Progetti personali](#progetti-personali)
5. [Aggiungere un nuovo progetto](#aggiungere-un-nuovo-progetto)
6. [Regole fondamentali](#regole-fondamentali)

---

## Struttura dei repository

### Progetto prodotto completo

Ogni prodotto ha **5 repository**:

| Repository | Contenuto |
|---|---|
| `{progetto}-site` | Sito web pubblico del prodotto |
| `{progetto}-frontend` | SPA utente finale |
| `{progetto}-backend` | API, business logic, server |
| `{progetto}-core` | Admin panel oppure libreria core (dipende dalla complessità) |
| branch su `shared-twenty-crm` | CRM custom derivato da Twenty CRM |

### Progetto piccolo / personale

Solo i componenti necessari:

| Repository | Contenuto |
|---|---|
| `{nome}-frontend` | opzionale |
| `{nome}-backend` | opzionale |

Privato, visibile solo al proprietario. Percorso di promozione a progetto ufficiale descritto sotto.

### Software trasversale

| Repository | OSS di origine | Uso |
|---|---|---|
| `shared-twenty-crm` | [twentyhq/twenty](https://github.com/twentyhq/twenty) | CRM customizzato per ogni progetto |
| `shared-keycloak` | [keycloak/keycloak](https://github.com/keycloak/keycloak) | SSO / IAM per ogni progetto |

### Nota su `core`

- **Progetto semplice** (es. Componi, AI SEO) → `core` è il pannello di amministrazione dell'app
- **Progetto complesso** (es. iSmartFrame) → `core` è un monorepo con package interni:

```
ismartframe-core/
  packages/
    orchestrator/    ← ReAct + Reflection engine
    gateway/         ← LLM Gateway
    agents/          ← Agent SDK, sub-agents
    shared/          ← utilities, tipi condivisi
```

---

## Come lavorare

### Branch strategy — identica per tutti i progetti

```
main          → codice stabile / produzione
develop       → integrazione continua, deploy staging automatico
feature/*     → lavoro individuale
hotfix/*      → fix urgente su produzione
```

### Flusso quotidiano

```bash
# 1. Parti sempre da develop aggiornato
git checkout develop
git pull origin develop

# 2. Crea il tuo branch
git checkout -b feature/tuonome-descrizione-task

# 3. Lavora, committa
git add .
git commit -m "feat: descrizione della modifica"

# 4. Pusha il branch
git push origin feature/tuonome-descrizione-task

# 5. Apri una Pull Request su GitHub: feature/* → develop
```

### Naming convention branch

| Pattern | Esempio | Quando |
|---|---|---|
| `feature/{nome}-{task}` | `feature/mario-login-oauth` | Nuova funzionalità |
| `fix/{nome}-{task}` | `fix/luca-null-pointer` | Bug fix su develop |
| `hotfix/{descrizione}` | `hotfix/payment-timeout` | Fix urgente su main |
| `chore/{nome}-{task}` | `chore/mario-upgrade-node` | Refactor, dipendenze |

### Regole PR

- Almeno **1 approvazione** per fare merge su `develop`
- Almeno **1 approvazione** per fare merge su `main`
- La **CI deve essere verde** prima del merge
- Il branch deve essere **aggiornato** rispetto al target

---

## Software trasversale

`shared-twenty-crm` e `shared-keycloak` sono fork di software OSS.
Ogni progetto ha le proprie customizzazioni su un branch dedicato.

### Struttura branch

```
upstream          ← mirror automatico dell'OSS originale (NON modificare mai)
base/main         ← base stabile verificata (solo Tech Lead fa merge qui)
ismartframe/main  ← customizzazioni iSmartFrame
componi/main      ← customizzazioni Componi
aiseo/main        ← customizzazioni AI SEO
feature/{p}-{t}   ← branch di lavoro individuali
```

### Come lavorare sulle customizzazioni

```bash
# Clona il repo shared
git clone https://github.com/ismartframe/shared-twenty-crm.git
cd shared-twenty-crm

# Parti dal branch del tuo progetto
git checkout componi/main
git pull origin componi/main

# Crea branch di lavoro
git checkout -b feature/componi-custom-pipeline

# ... lavora ...

# Apri PR: feature/componi-custom-pipeline → componi/main
```

### Ciclo di aggiornamento upstream

1. **Ogni lunedì** (automatico) — la GitHub Action sincronizza `upstream` dall'OSS originale
2. **Apertura PR automatica** — `upstream → base/main` con il diff pronto per review
3. **Tech Lead fa review e merge** — verifica breaking changes
4. **Ogni team aggiorna il proprio branch** — `rebase {progetto}/main su base/main`

> ⚠️ **Non fare mai commit direttamente sul branch `upstream`** — viene sovrascritto automaticamente.

---

## Progetti personali

Ogni sviluppatore può avere repository privati per i propri progetti.

**Naming:** `{tuonome}-{progetto}` (es. `mario-tool-interno`)

**Struttura minima:** solo `frontend` e/o `backend` — aggiungi solo quello che serve.

### Percorso di promozione a progetto ufficiale

| Step | Stato | Cosa fare |
|---|---|---|
| **1 — Privato** | Solo tu lavori | Nessuna regola obbligatoria — lavoro libero |
| **2 — Condiviso** | Il team inizia a contribuire | Abilita Branch Protection su `main` (PR + 1 review) |
| **3 — Ufficiale** | Asset del team | Rinomina, crea team dedicato, struttura branch completa + CI/CD |

Parla con il Tech Lead quando vuoi promuovere un progetto personale.

---

## Aggiungere un nuovo progetto

Procedura standard — tempo stimato: 30 minuti:

1. **Crea GitHub Team** `team-{nomeprogetto}` e assegna i membri
2. **Crea i repository** con naming `{nomeprogetto}-site`, `-frontend`, `-backend`, `-core`
3. **Aggiungi topic** `project:{nomeprogetto}` su ogni repo
4. **Applica Branch Protection** identica alla tabella in questo documento
5. **Crea branch** `ismartframe/main`, `componi/main`, `aiseo/main` nei repo `shared-*` se il progetto li usa
6. **Aggiorna CODEOWNERS** con il nuovo percorso

---

## Regole fondamentali

> Queste regole sono **tecnicamente imposte** dal sistema — non è una questione di disciplina personale.

| ❌ NON fare | ✅ Fare invece |
|---|---|
| Push diretto su `main` | Apri una PR da `feature/*` |
| Push diretto su `develop` | Apri una PR da `feature/*` |
| Force push su `main` o `develop` | Non è possibile — il server rifiuta |
| Commit direttamente su `upstream` | Lavora su `feature/{progetto}-*` |
| Merge senza review | Aspetta almeno 1 approvazione |
| Merge con CI rossa | Risolvi prima i test |

---

## GitHub Teams

| Team | Accesso a |
|---|---|
| `owners` | Admin su tutto |
| `team-ismartframe` | `ismartframe-*` Write, tutti gli altri Read |
| `team-componi` | `componi-*` Write |
| `team-aiseo` | `aiseo-*` Write |
| `team-shared` | `shared-*` Write (base/main solo Tech Lead) |
| `team-personal` | `{tuonome}-*` Write solo tu |

---

*Governance Repository GitHub — iSmartFrame v1.2 — Aprile 2026*
*Documento completo: SharePoint teamDEV → Documentazione → Governance*
