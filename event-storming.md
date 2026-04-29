# Event Storming: Kalkulator Sederhana

## Overview
Aplikasi kalkulator sederhana yang mendukung operasi aritmatika dasar: penjumlahan, pengurangan, perkalian, dan pembagian.

---

## Actors (Pengguna)
- **User** — pengguna yang melakukan perhitungan

---

## Domain Events (Kejadian yang terjadi)

### Input & Kalkulasi
- `NumberEntered` — user memasukkan angka pertama
- `OperatorSelected` — user memilih operator (+, -, ×, ÷)
- `SecondNumberEntered` — user memasukkan angka kedua
- `CalculationRequested` — user menekan tombol "="
- `ResultCalculated` — hasil kalkulasi berhasil dihitung
- `ResultDisplayed` — hasil ditampilkan di layar

### Error & Edge Cases
- `DivisionByZeroAttempted` — user mencoba membagi dengan angka 0
- `ErrorDisplayed` — pesan error ditampilkan
- `InputCleared` — user mereset/menghapus input (tombol C)
- `LastEntryDeleted` — user menghapus digit terakhir (tombol backspace)

### History
- `CalculationSaved` — hasil kalkulasi disimpan ke history
- `HistoryViewed` — user melihat riwayat kalkulasi
- `HistoryCleared` — user menghapus semua riwayat

---

## Commands (Perintah yang memicu event)

| Command | Triggered By | Resulting Event |
|---|---|---|
| `EnterNumber` | User | `NumberEntered` |
| `SelectOperator` | User | `OperatorSelected` |
| `Calculate` | User (tekan "=") | `CalculationRequested` → `ResultCalculated` |
| `ClearInput` | User (tekan "C") | `InputCleared` |
| `DeleteLastEntry` | User (tekan "⌫") | `LastEntryDeleted` |
| `ViewHistory` | User | `HistoryViewed` |
| `ClearHistory` | User | `HistoryCleared` |

---

## Aggregates (Entitas inti)

### Calculator
- State: `currentInput`, `operator`, `previousInput`, `result`
- Menangani: semua operasi kalkulasi dan validasi input

### CalculationHistory
- State: `entries[]` (list hasil kalkulasi)
- Menangani: penyimpanan dan pengambilan riwayat

---

## Policies / Business Rules

- **DivisionByZero Policy**: Jika operator `÷` dan angka kedua adalah `0` → tampilkan error "Cannot divide by zero"
- **MaxDigit Policy**: Input angka dibatasi maksimal 10 digit
- **ChainCalculation Policy**: Hasil kalkulasi dapat langsung digunakan sebagai input operasi berikutnya
- **AutoSave Policy**: Setiap `ResultCalculated` otomatis memicu `CalculationSaved`

---

## Flow Utama

```
User
 │
 ├─[EnterNumber]──────────► NumberEntered
 │                               │
 ├─[SelectOperator]──────► OperatorSelected
 │                               │
 ├─[EnterNumber]──────────► SecondNumberEntered
 │                               │
 └─[Calculate]────────────► CalculationRequested
                                 │
                    ┌────────────┴────────────┐
                    │                         │
              [valid input]           [division by zero]
                    │                         │
            ResultCalculated          DivisionByZeroAttempted
                    │                         │
            ResultDisplayed           ErrorDisplayed
                    │
            CalculationSaved
```

---

## UI Components yang Dibutuhkan

| Komponen | Fungsi |
|---|---|
| `Display` | Menampilkan input dan hasil |
| `NumberPad` | Tombol angka 0-9 dan desimal |
| `OperatorButtons` | Tombol +, -, ×, ÷ |
| `EqualsButton` | Tombol = untuk kalkulasi |
| `ClearButton` | Tombol C untuk reset |
| `BackspaceButton` | Tombol ⌫ untuk hapus digit terakhir |
| `HistoryPanel` | Panel riwayat kalkulasi |

---

## Technical Notes

- **State Management**: Local state (useState) cukup untuk kalkulator sederhana
- **Persistence**: localStorage untuk menyimpan history
- **Validation**: Handle edge cases (division by zero, overflow)
- **Framework**: React dengan TypeScript

