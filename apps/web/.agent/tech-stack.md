# Frontend Tech Stack & Guidelines (Vue.js)

**Scope:** `apps/web` ONLY. Diese Regeln gelten für den gesamten Frontend-Code.

## 1. Core Stack
- **Framework:** Vue 3 (mit Vite).
- **Sprache:** TypeScript (Strict Mode).
- **Styling:** Tailwind CSS.
- **Icons:** `lucide-vue-next`.

## 2. Component Architecture & Syntax
- [MUST] Nutze AUSSCHLIESSLICH die Composition API mit `<script setup lang="ts">`.
- [NEVER] nutze die alte Options API (`export default { data() {...} }`).
- Halte Komponenten klein. Wenn eine `.vue`-Datei länger als 150 Zeilen wird, lagere Logik in Composables (in `src/composables/`) oder UI-Teile in Sub-Komponenten aus.
- Nutze `defineProps` und `defineEmits` strikt typisiert.

## 3. State Management & Data Fetching
- **Server State (API):** [MUST] Nutze `TanStack Query for Vue` (Vue Query) für alle API-Aufrufe zum Rust-Backend. Implementiere Caching, Loading-States und Error-Handling darüber.
- **Client State (UI):** Nutze `Pinia` für globalen UI-State (z.B. Theme, Sidebar-Status, User-Session). Vermeide es, Backend-Daten im Pinia-Store zu duplizieren.
- **Composables:** Nutze `VueUse` für Standard-Browser-APIs (z.B. `useWindowSize`, `useLocalStorage`).

## 4. The Bridge (Monorepo Integration)
- [MUST] Importiere DTOs, API-Responses und Request-Typen AUSSCHLIESSLICH aus `packages/shared/types/` (diese werden vom Rust-Backend generiert).
- [NEVER] definiere Backend-Typen im Frontend neu. Wenn ein Typ im Frontend fehlt, musst du ihn zuerst im Rust-Backend definieren und neu generieren lassen.

## 5. KI / Vibe-Coding Richtlinien für Vue
- Bevorzuge bedingtes Rendering (`v-if`, `v-else`) und Listen (`v-for`) im Template klar lesbar.
- Extrahiere komplexe Tailwind-Klassen-Ketten NICHT in `<style>`-Blöcke. Behalte sie im Template (Utility-First), es sei denn, es handelt sich um hochgradig dynamische Klassen, dann nutze `computed()`.
- Wenn du eine neue Komponente erstellst, überlege zuerst: Ist es eine "Dumb Component" (nur UI, bekommt Daten über Props) oder eine "Smart Component" (fetcht eigene Daten über Vue Query)? Mach diesen Unterschied im Code klar.