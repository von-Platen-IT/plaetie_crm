---
description: Orchestriert die Erstellung eines neuen Features über alle Schichten (Rust Backend -> Shared Types -> Vue Frontend) hinweg.
---

# Full-Stack Feature Workflow
**Trigger:** `/new-feature [Feature Description]`
**Description:** Orchestriert die Erstellung eines neuen Features über alle Schichten (Rust Backend -> Shared Types -> Vue Frontend) hinweg.

## Step 1: Analyse & Planung
- Lies die Anforderung des Users.
- Identifiziere, welche Rust-Module (`apps/backend/src/modules/`) und Vue-Views/Components (`apps/web/src/`) betroffen sind.
- Erstelle einen extrem kurzen, stichpunktartigen Plan (max. 3 Zeilen) im Chat, bevor du Code schreibst.

## Step 2: Datenbank & DTOs (Rust Backend)
- **SeaORM:** Erstelle oder update die Entitäten in `src/entities/`. Generiere die dazugehörige Migration.
- **DTOs:** Erstelle die Request- und Response-Objekte in `dto.rs` des jeweiligen Moduls. 
- [MUST] Vergiss nicht `#[derive(TS)]` und konfiguriere den Export nach `packages/shared/types/`.

## Step 3: Backend Logik (Rust)
- Implementiere die Geschäftslogik in `service.rs`.
- Erstelle den HTTP-Endpunkt in `controller.rs` (Axum) und nutze die neuen DTOs.
- [MUST] Stelle sicher, dass der Code kompiliert (`cargo check`).

## Step 4: The Bridge Sync (WICHTIG!)
- Bevor du das Frontend anfasst, musst du sicherstellen, dass die TypeScript-Interfaces generiert wurden.
- Führe den Rust-Befehl aus (z.B. `cargo test` oder deinen spezifischen `ts-rs` Export-Befehl), damit die neuen `.ts` Dateien in `packages/shared/types/` landen.
- Lies kurz die generierte `.ts` Datei, um den genauen Type-Namen für das Frontend zu kennen.

## Step 5: Frontend Implementierung (Vue 3)
- Wechsle in den Ordner `apps/web/`.
- Importiere die frisch generierten Typen aus `packages/shared/types/`.
- **API Call:** Schreibe die Vue Query (`useQuery` oder `useMutation`), um den neuen Rust-Endpunkt anzusprechen.
- **UI:** Baue die UI-Komponente mit Vue 3 `<script setup>` und Tailwind CSS.

## Step 6: Type Safety Check
- [MUST] Führe im `apps/web` Ordner den TypeScript-Check aus (z.B. `vue-tsc --noEmit`), um zu garantieren, dass Frontend und Backend Typen 100% synchron sind.
- Wenn Fehler auftreten, fixe sie sofort.