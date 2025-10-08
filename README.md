# WhereGroup Project Template - Setup Guide

🎯 **Dieses Template stellt automatisch sicher, dass GitHub Copilot die WhereGroup-Regeln in jedem neuen Projekt kennt.**

---

## 🚀 Quick Start (30 Sekunden)

### 1. Neues Projekt von diesem Template erstellen

**Option A: GitHub UI**
1. Gehe zu diesem Repository
2. Klick "Use this template" → "Create a new repository"
3. Wähle Namen + Visibility → "Create repository"
4. Clone dein neues Projekt: `git clone https://github.com/fhaefker/DEIN-NEUES-REPO.git`

**Option B: GitHub CLI**
```bash
gh repo create DEIN-NEUES-PROJEKT --template fhaefker/wheregroup-knowledge-base --private
cd DEIN-NEUES-PROJEKT
```

### 2. Verifiziere die Regel-Integration

```bash
# Prüfe ob Copilot-Instructions vorhanden sind
ls -la .github/copilot-instructions.md

# Prüfe Zeilenanzahl (sollte ~638 Zeilen sein)
wc -l .github/copilot-instructions.md
```

**Erwartete Ausgabe:**
```
638 .github/copilot-instructions.md
```

### 3. Teste GitHub Copilot

Öffne eine neue Datei in VS Code und frage Copilot:

```
Q: "Welche 5 Regeln muss ich bei WhereGroup immer einhalten?"
```

**Erwartete Antwort:** Copilot sollte alle 5 Rules (Never Forget, Never Lie, Never Crash, Learn Completely, Absolute Immutability) referenzieren.

---

## 📋 Was ist automatisch konfiguriert?

### ✅ GitHub Copilot Integration

**Datei:** `.github/copilot-instructions.md`

- Wird **automatisch von GitHub Copilot gelesen**
- Enthält alle 5 WhereGroup Rules + 7 Core Values
- Wird **wöchentlich automatisch aktualisiert** (siehe unten)

**Kein manueller Setup erforderlich!** Copilot kennt die Regeln sofort nach Template-Instantiierung.

---

### ✅ Automatische Updates (GitHub Actions)

**Workflow:** `.github/workflows/sync-rules.yml`

**Funktionsweise:**
1. **Wöchentlich:** Jeden Montag 00:00 UTC
2. **Bei Changes:** Automatisch wenn `COPILOT_ESSENTIALS.md` geändert wird
3. **Manuell:** Über GitHub Actions UI triggerbar

**Was wird synchronisiert:**
- Source: `COPILOT_ESSENTIALS.md` (Master-Version der Regeln)
- Target: `.github/copilot-instructions.md` (von Copilot gelesen)

**Integritätsprüfung:**
- Verifiziert mindestens 600 Zeilen (Schutz gegen versehentliche Löschung)
- Prüft Vorhandensein aller 5 Rules
- Commit nur wenn Änderungen vorhanden

**Warum automatisch?**
- **RULE 1 (Never Forget):** Stellt sicher dass Copilot immer aktuelle Regeln hat
- **RULE 3 (Never Crash):** Automated Monitoring reduziert manuelle Fehler

---

### ✅ VS Code Snippets

**Datei:** `.vscode/wheregroup.code-snippets`

**Verfügbare Snippets:**

#### 1. `wg-check` - Vollständige Compliance-Checkliste
```
Trigger: Tippe "wg-check" in Markdown/Code
Output: 45-Punkte Checkliste (5 Rules × 3 Dimensions × 3 Checks)
Use Case: Vor kritischen Changes, Code Reviews, Merges
```

**Beispiel:**
```markdown
## 🔍 WhereGroup RULE COMPLIANCE CHECK
**Checkpoint Zeitpunkt:** 2025-10-08 14:30
...
✅ RULE 1: NEVER FORGET (3 Dimensionen)
- [ ] Habe ich ALLE relevanten Dateien gelesen?
...
```

#### 2. `wg-rules` - Quick Reference
```
Trigger: Tippe "wg-rules"
Output: Kompakte 1-Seiten-Übersicht aller 5 Rules
Use Case: Schnelle Erinnerung während Entwicklung
```

#### 3. `wg-emergency` - Incident Protocol
```
Trigger: Tippe "wg-emergency"
Output: Notfall-Protokoll Template bei Regel-Verletzung
Use Case: Root Cause Analysis, Lesson Learned Dokumentation
```

---

## 🔧 Erweiterte Konfiguration (Optional)

### Continue.dev Integration (Für periodische Re-Injection)

**Problem:** Bei sehr langen Conversations könnte Copilot die Regeln "vergessen" (Instruction Drift).

**Lösung:** Continue.dev kann Regeln periodisch re-injizieren.

**Setup (5 Minuten):**

1. **Installiere Continue.dev Extension:**
   ```bash
   code --install-extension continue.continue
   ```

2. **Konfiguriere `~/.continue/config.ts`:**
   ```typescript
   export function modifyConfig(config: Config): Config {
     config.systemMessage = `
   ${fs.readFileSync('.github/copilot-instructions.md', 'utf-8')}
   
   CRITICAL: These rules are ABSOLUTE and must be followed in EVERY response.
   `;
     
     // Re-inject alle 5 Prompts
     config.contextProviders = [
       {
         name: "wheregroup-rules",
         params: { interval: 5 }
       }
     ];
     
     return config;
   }
   ```

3. **Teste:**
   - Starte lange Conversation (20+ Prompts)
   - Frage zwischendurch: "Welche Regeln gelten?"
   - Verify: Rules sollten auch nach 20+ Prompts vollständig bekannt sein

**Wann nützlich:**
- Sehr lange Debugging-Sessions
- Pair Programming über mehrere Stunden
- Komplexe Refactorings mit vielen Iterationen

**Wann NICHT nötig:**
- Standard-Entwicklung (GitHub Copilot reicht)
- Kurze Sessions (<10 Prompts)
- Keine kritischen Regel-Compliance-Anforderungen

---

## 📊 Verifikation & Testing

### Test 1: Copilot kennt Rules

**In VS Code:**
1. Öffne beliebige Datei
2. Frage Copilot: "Was ist RULE 2?"
3. **Erwartung:** Beschreibung von "Never Lie" mit 3 Dimensionen

### Test 2: Copilot wendet Rules an

**Prompt:**
```
Erstelle eine Funktion die Daten aus einer Datei lädt.
Beachte dabei die WhereGroup Rules.
```

**Erwartetes Verhalten:**
- Funktion prüft ob Datei existiert (RULE 3: Check Limits)
- Fehlerbehandlung mit aussagekräftigen Messages (RULE 2: Never Lie)
- Keine Placeholder-Kommentare wie "// ... existing code ..." (RULE 4: Learn Completely)

### Test 3: GitHub Action läuft

**Manueller Trigger:**
1. Gehe zu: `Actions` Tab im GitHub-Repo
2. Wähle "Sync WhereGroup Rules to Copilot"
3. Klick "Run workflow"
4. **Erwartung:** ✅ Grün, "No changes" oder "Sync successful"

**Automatischer Trigger (wöchentlich):**
- Check jeden Montag ob Action gelaufen ist
- Bei Failure: Check Logs in Actions Tab

---

## 🛡️ Schutz-Mechanismen (Built-in)

### 1. Pre-commit Hook (Optional - Empfohlen)

**Verhindert versehentliche Änderungen an kritischen Dateien.**

**Installation:**
```bash
# Erstelle Pre-Commit Hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

# RULE 5 (Immutability): Kritische Dateien schützen
PROTECTED_FILES=(
  "COPILOT_ESSENTIALS.md"
  ".github/copilot-instructions.md"
)

for file in "${PROTECTED_FILES[@]}"; do
  if git diff --cached --name-only | grep -q "^$file$"; then
    echo "🚨 RULE 5 VIOLATION: $file ist geschützt!"
    echo "❌ Commit abgelehnt - diese Datei darf nicht manuell geändert werden"
    echo "ℹ️  Änderungen nur über sync-rules.yml Workflow erlaubt"
    exit 1
  fi
done

exit 0
EOF

chmod +x .git/hooks/pre-commit
```

**Teste:**
```bash
# Versuche geschützte Datei zu ändern
echo "test" >> COPILOT_ESSENTIALS.md
git add COPILOT_ESSENTIALS.md
git commit -m "test"

# Erwartung: ❌ Commit wird blockiert
```

### 2. Dateischutz (Read-Only)

**Verhindert versehentliche Editierung in VS Code.**

```bash
# Mache kritische Dateien read-only
chmod 444 COPILOT_ESSENTIALS.md
chmod 444 .github/copilot-instructions.md
```

**Resultat:**
- VS Code zeigt "Read-Only" in Statusbar
- Editierung nur nach expliziter Freigabe möglich

---

## 📚 Weitere Dokumentation

### Vollständige Rule-Dokumentation

Für tiefes Verständnis der 5 Rules:

```bash
# Lies die Master-Dokumentation
cat COPILOT_ESSENTIALS.md

# Oder öffne in VS Code
code COPILOT_ESSENTIALS.md
```

**Inhalt:**
- Alle 5 Rules mit 3 Dimensionen (BEFORE/DURING/AFTER)
- Self-Check Questions
- WhereGroup 7 Core Values
- Emergency Protocols
- Real-world Examples

### Incident Catalog (Lessons Learned)

**Wenn vorhanden im Template:**
- `00_LESSONS_LEARNED_CATALOG.md` - Historische Incidents
- Zeigt was bei Regel-Verletzungen passieren kann
- Best Practices aus echten Projekten

---

## 🚨 Troubleshooting

### Problem: Copilot kennt Rules nicht

**Symptom:** Copilot antwortet nicht regel-konform.

**Lösungen:**

1. **Prüfe Datei-Existenz:**
   ```bash
   ls -la .github/copilot-instructions.md
   # Sollte ~638 Zeilen haben
   ```

2. **Prüfe Datei-Inhalt:**
   ```bash
   grep "RULE 1" .github/copilot-instructions.md
   # Sollte Treffer liefern
   ```

3. **Manueller Sync:**
   ```bash
   cp COPILOT_ESSENTIALS.md .github/copilot-instructions.md
   git add .github/copilot-instructions.md
   git commit -m "fix: Manual sync of Copilot instructions"
   git push
   ```

4. **VS Code Reload:**
   - Cmd/Ctrl+Shift+P → "Reload Window"
   - Copilot neu initialisieren

### Problem: GitHub Action schlägt fehl

**Symptom:** Action zeigt ❌ in Actions Tab.

**Debug:**

1. **Check Logs:**
   - Gehe zu Actions Tab
   - Klick auf failed run
   - Lese Error Message

2. **Häufige Ursachen:**
   - `COPILOT_ESSENTIALS.md` fehlt → Erstelle Datei
   - Permissions fehlen → Check `permissions: contents: write` in Workflow
   - Datei zu klein → Prüfe ob vollständig (min. 600 Zeilen)

3. **Test lokal:**
   ```bash
   # Simuliere Action lokal
   cp COPILOT_ESSENTIALS.md .github/copilot-instructions.md
   
   # Prüfe Zeilenzahl
   wc -l .github/copilot-instructions.md
   # Sollte ~638 sein
   ```

### Problem: Snippet `wg-check` funktioniert nicht

**Symptom:** Nichts passiert beim Tippen von "wg-check".

**Lösungen:**

1. **Prüfe Snippet-Installation:**
   ```bash
   ls -la .vscode/wheregroup.code-snippets
   ```

2. **Reload VS Code:**
   - Cmd/Ctrl+Shift+P → "Reload Window"

3. **Prüfe Scope:**
   - Snippets funktionieren in `.md`, `.ts`, `.js`, `.py` Dateien
   - Nicht in Binärdateien oder exotischen Formaten

4. **Manuell testen:**
   - Öffne neue Markdown-Datei
   - Tippe "wg-" → Autocomplete sollte Snippets zeigen

---

## 🎯 Best Practices

### Wann `wg-check` verwenden?

**IMMER vor:**
- Git commits mit >100 Zeilen Änderungen
- Merge Requests / Pull Requests
- Deployment zu Production
- Refactorings kritischer Code-Bereiche

**OPTIONAL vor:**
- Kleineren Bug Fixes (<20 Zeilen)
- Dokumentations-Updates
- Rein visuellen Änderungen (CSS, Styling)

### Wann `wg-emergency` verwenden?

**IMMER bei:**
- RULE 2 Violation (Never Lie) → Zero Tolerance!
- Datenverlust (RULE 1 Violation)
- Token Crash (RULE 3 Violation)
- Production Incident durch Regel-Verletzung

**NICHT verwenden für:**
- Normale Bugs (ohne Regel-Bezug)
- Feature Requests
- Performance Issues (außer wenn durch RULE 3 verursacht)

---

## 📞 Support & Feedback

### Fragen zum Template

**Erstelle Issue im Template-Repository:**
- https://github.com/fhaefker/wheregroup-knowledge-base/issues

**Kategorien:**
- `bug` - Template funktioniert nicht wie beschrieben
- `enhancement` - Verbesserungsvorschlag
- `documentation` - README unklar/unvollständig
- `question` - Allgemeine Fragen

### Feedback zu den Rules

**Wenn Rules in der Praxis Probleme verursachen:**

1. Dokumentiere konkrete Situation
2. Beschreibe welche Rule konfliktiert
3. Schlage Alternative vor
4. Erstelle Issue mit Label `rule-feedback`

**Wichtig:** Rules sind per RULE 5 immutable, aber Feedback hilft bei:
- Klareren Erklärungen
- Besseren Beispielen
- Zusätzlichen Tools/Mechanismen

---

## 🎓 Lernressourcen

### Neu bei WhereGroup?

**Start hier:**
1. Lies `COPILOT_ESSENTIALS.md` (30 Min)
2. Teste alle 3 Snippets (`wg-check`, `wg-rules`, `wg-emergency`)
3. Erstelle Dummy-Projekt und teste Copilot Integration
4. Führe ersten `wg-check` durch

**Nach 1 Woche:**
- Rules sollten in Muscle Memory übergehen
- `wg-check` wird zur Routine vor Commits
- Copilot-Antworten intuitiv regel-konform

### Vertiefung

**Vollständige Dokumentation:**
- `00_ABSOLUTE_RULES.md` (4183 Zeilen, Master-Dokument)
- `00_WHEREGROUP_MISSION_VISION_LEITBILD.md` (473 Zeilen, Core Values)

**Historische Context:**
- `00_LESSONS_LEARNED_CATALOG.md` (Falls im Template enthalten)
- Zeigt Incidents und deren Konsequenzen

---

## ✅ Checkliste: Setup Komplett?

Nach Template-Instantiierung sollten alle Punkte ✅ sein:

**Basis-Setup:**
- [ ] Repository von Template erstellt
- [ ] `.github/copilot-instructions.md` existiert (~638 Zeilen)
- [ ] `.github/workflows/sync-rules.yml` existiert
- [ ] `.vscode/wheregroup.code-snippets` existiert
- [ ] `COPILOT_ESSENTIALS.md` existiert

**Verifikation:**
- [ ] Copilot kennt RULE 1-5 (Test durchgeführt)
- [ ] `wg-check` Snippet funktioniert
- [ ] `wg-rules` Snippet funktioniert
- [ ] `wg-emergency` Snippet funktioniert
- [ ] GitHub Action manuell getriggert (✅ grün)

**Optional (Empfohlen):**
- [ ] Pre-commit Hook installiert
- [ ] Kritische Dateien read-only (chmod 444)
- [ ] Continue.dev konfiguriert (bei Bedarf)

**Dokumentation:**
- [ ] `COPILOT_ESSENTIALS.md` gelesen
- [ ] Dieses README gelesen
- [ ] Team über neue Rules informiert

---

## 🚀 Du bist ready!

**Alle Checks ✅?**

→ Starte Entwicklung mit automatischem Rule Enforcement!

**GitHub Copilot wird dich automatisch erinnern wenn du gegen Rules verstoßen willst.**

**Beispiel:**
```
Du: "Schreib mir eine Funktion mit ... existing code ..."
Copilot: "⚠️ RULE 4 Violation: Keine Placeholders erlaubt. Ich schreibe die vollständige Funktion."
```

**Viel Erfolg! 🎯**

---

**Last Updated:** 2025-10-08  
**Template Version:** 1.0.0  
**Maintained by:** WhereGroup Knowledge Base Team
