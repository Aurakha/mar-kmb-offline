# MAR KMB Offline (PWA)

Companion PWA offline untuk **Mechanic Activity Report PT KMB (ver 2)**.
Diselaraskan untuk terhubung langsung dengan backend Google Apps Script `D:\KMB`.

- **Script ID Backend:** `17sBKK-x3qXzL7tRlejsXE1kQXGwF-CQtCoCdhA4ZWE4_E2NSq0ZUwtqo`
- **Web App URL Target:** `https://script.google.com/macros/s/AKfycbwlwlQvOGVF6FdKkYRNlbgdJCets5L-0AfufMB4_79_HzvoQkeE9aZAqkKZiXCZHXnG6Q/exec`
- **Backend Protocol:** `ApiService.handleApiPost`, kontrak POST `text/plain` JSON `{token, action, op_id, data}`.
- **Login:** Token dari sheet `ApiTokens` (sama dgn web). Identitas selalu dari token.
- **Offline Engine:** CACHE → ANTRE (IndexedDB `mar_kmb_v1`) → SINKRON (Background Sync / FIFO). Idempoten via `op_id` + `ProcessedOps` (dedup di backend).
- **Peran UI:** 
  - Mekanik = Isi Work Order + Live Timer + Request Transfer + Lapor Expired.
  - L1 (Supervisor) / L2 (Superintendent) = Buat WO + Approval + Override (Base Points, Target Hours, Tim Mekanik) + Approve/Reject Transfer + Reopen Expired + Cancel WO.
  - Foreman = Buat WO saja.

## Karakteristik Model Bisnis KMB
- Tanpa section/scope, tanpa job-katalog.
- `part_type` wajib (baru / repair / canibal).
- Pengisian job manual via `COM-OTHERS` (deskripsi, base points, target hours, unit factor).
- MTBF mati, tema warna biru KMB.
- Fitur **Override L1/L2 aktif** (dukungan FIFO outbox agar override terkirim sebelum approve).

## Hosting & Rilis
Dapat di-deploy via GitHub Pages (branch `main` / root) atau Web Server statis pilihan.
Bump `CACHE` di `sw.js` + `APP_VERSION` di `app.js` bersamaan tiap kali ada rilis pembaruan.
