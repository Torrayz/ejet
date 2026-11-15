# Analisis Sistem Informasi Koperasi Simpan Pinjam

Dokumentasi Praktik Demonstrasi Bagian #1 untuk unit kompetensi **J.620100.002.01 – Menganalisis Skalabilitas Perangkat Lunak** (LSP UNPAM 2026/2027).

## Skenario

Koperasi **Sejahtera Mandiri** mengelola:
- Simpanan Pokok, Wajib, dan Sukarela.
- Pinjaman berbunga tetap dengan tenor bulanan.
- Pembayaran angsuran.
- Pencatatan transaksi oleh petugas/admin yang terautentikasi.

## Struktur Repository

- `docs/ANALISIS-SISTEM.md` — dokumentasi analisis kebutuhan, aturan bisnis, rancangan data, validasi, keamanan, skalabilitas, dan skenario pengujian.
- `diagrams/ERD.drawio` — Entity Relationship Diagram, dapat diedit di diagrams.net.
- `diagrams/LRS.drawio` — Logical Record Structure, dapat diedit di diagrams.net.
- `diagrams/DFD-Level-0.drawio` — Context Diagram, dapat diedit di diagrams.net.
- `diagrams/mermaid.md` — sumber diagram Mermaid.

## Entitas Utama

1. `users`
2. `anggota`
3. `jenis_simpanan`
4. `simpanan`
5. `pinjaman`
6. `angsuran`

## Catatan Analisis

Formula bunga tetap, aturan denda keterlambatan, kebijakan kelebihan pembayaran, dan kemungkinan penarikan simpanan belum dirinci dalam skenario tugas. Hal-hal tersebut ditandai sebagai keputusan bisnis yang harus dikonfirmasi, bukan diisi dengan asumsi.

## Membuka Diagram

Unduh file `.drawio` atau buka [diagrams.net](https://app.diagrams.net/) lalu pilih **File → Open From → Device**.

---
Dokumen disusun berdasarkan skenario tugas praktik yang diberikan.
