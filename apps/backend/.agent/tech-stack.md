# Backend Tech Stack & Guidelines (Rust)

**Scope:** `apps/backend` ONLY. Diese Regeln gelten für den gesamten Rust-Code.

## 1. Core Stack
- **Sprache:** Rust (Latest Stable Edition).
- **Web Framework:** `axum` in Kombination mit `tokio`.
- **ORM:** `sea-orm` (Async, dynamisch typisiertes ORM).
- **Serialisierung:** `serde` (Serialize, Deserialize).

## 2. Modulare Architektur (Feature-Based)
Strukturiere den Code strikt modular nach Features (Domain-Driven), NICHT flach nach technischen Schichten.
Jedes Feature (z.B. `users`, `products`) bekommt ein eigenes Modul in `src/modules/` mit exakt dieser Struktur:
- `mod.rs` (Exportiert das Modul und registriert die Axum-Router).
- `controller.rs` (Axum Handler / HTTP-Endpunkte).
- `service.rs` (Reine Geschäftslogik. Darf nichts von HTTP wissen).
- `repository.rs` (Datenbank-Interaktionen mit SeaORM).
- `dto.rs` (Data Transfer Objects für Request/Response).

## 3. ORM & Database Rules (SeaORM)
- [MUST] Alle Datenbank-Entitäten (`Entities`) liegen in einem separaten Ordner `src/entities/`.
- [NEVER] schreibe rohes SQL, es sei denn, es ist für eine extrem komplexe Query zwingend erforderlich. Nutze die SeaORM Query Builder API.
- Datenbank-Zugriffe erfolgen AUSSCHLIESSLICH im `repository.rs` der jeweiligen Module. Der `controller.rs` ruft nur den `service.rs` auf.

## 4. Error Handling
- [NEVER] nutze `unwrap()` oder `expect()` im Produktionscode. Behandle Fehler immer graceful mit `Result<T, E>`.
- Nutze das Crate `thiserror` für Domain-Errors und implementiere `axum::response::IntoResponse`, um Rust-Errors zentral in standardisierte HTTP-JSON-Responses (z.B. Problem Details) umzuwandeln.

## 5. Monorepo Integration (The Bridge)
- [MUST] Alle DTOs in `dto.rs`, die ans Frontend gesendet oder von dort empfangen werden, müssen mit `#[derive(TS)]` (aus dem Crate `ts-rs`) markiert werden.
- Konfiguriere `ts-rs` so, dass die generierten TypeScript-Dateien automatisch in `packages/shared/types/` abgelegt werden. Das ist unser "Shared Contract" für das Frontend.

## 6. KI / Vibe-Coding Richtlinien für Rust
- **Lifetimes vs. Cloning:** Bevorzuge in der Geschäftslogik (`service.rs`) oft `.clone()`, anstatt komplexe explizite Lifetimes (`'a`) durch den gesamten Code zu ziehen, es sei denn, du befindest dich auf einem extrem performancekritischen Pfad. Das hält die Architektur simpel und wartbar.
- Optimiere Code erst, wenn er kompiliert und die Tests grün sind.