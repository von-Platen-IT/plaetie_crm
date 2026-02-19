---
trigger: always_on
---

# GLOBAL ARCHITECTURE & SYSTEM RULES
Diese Datei definiert die unumstößlichen Architektur-Regeln für dieses Monorepo. 
Lies diese Regeln, BEVOR du Code schreibst, analysierst oder refaktorierst.

## 1. SYSTEM ARCHITECTURE (CLIENT-SERVER)
Dieses Projekt ist eine verteilte Webanwendung. Es gibt drei strikt getrennte Domänen:
- **[FRONTEND / CLIENT]**: Reine Präsentationsschicht. Versteht UI, UX und lokales State-Management.
- **[BACKEND / SERVER]**: Geschäftslogik, API-Bereitstellung, Authentifizierung und Autorisierung.
- **[DATABASE]**: Persistenzschicht. Die einzige Source of Truth für gespeicherte Daten.

## 2. DIRECTORY ROUTING & SCOPE
Du [MUST] den Code immer in den korrekten Scopes platzieren:
- `apps/web/` -> Nur Frontend-Code. Darf NIEMALS direkt auf die Datenbank zugreifen.
- `apps/backend/` -> Nur Backend-Code. Darf keine UI-Komponenten oder Client-Libraries enthalten.
- `packages/shared/` -> Die Brücke. Hier liegen geteilte Typen, API-Contracts und Validierungsschemata (z.B. Zod/Interfaces), die von BEIDEN konsumiert werden.
- `packages/database/` (oder Backend-intern) -> Datenbank-Schemas und Migrationen.

## 3. THE FULL-STACK IMPLEMENTATION FLOW
Wenn du den Auftrag erhältst, ein neues Feature (Full-Stack) zu bauen, folge IMMER dieser Reihenfolge. Überspringe [NEVER] einen Schritt:
1. **Shared Contract:** Definiere zuerst das Datenmodell und den API-Contract in `packages/shared/`.
2. **Database:** Passe das Datenbankschema an und erstelle (falls nötig) eine Migration.
3. **Backend:** Implementiere die API-Route, die Validation (traue niemals dem Client-Input!) und die Geschäftslogik.
4. **Frontend:** Konsumiere die API, baue das UI und verknüpfe es mit dem lokalen State.

## 4. SECURITY BOUNDARIES
- [NEVER] speichere oder logge Secrets (API Keys, Passwörter) im Frontend.
- [MUST] validiere JEDEN eingehenden Request im Backend gegen das in `shared` definierte Schema.
- [MUST] setze CORS und Authentifizierungs-Checks im Backend um, bevor Geschäftslogik ausgeführt wird.

## 5. AI OUTPUT CONSTRAINTS
- Antworte präzise. Kein "Yapping" (keine unnötigen Erklärungen, "Ich habe verstanden", etc.).
- Bevorzuge inkrementelle Änderungen. Generiere nicht die ganze Datei neu, wenn du nur eine Zeile änderst.
- Wenn eine Änderung in einer Domäne (z.B. Backend API) eine andere Domäne (z.B. Frontend Fetch-Call) bricht, [MUST] du beide zeitgleich fixen.