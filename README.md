# Sega Mega Drive / Genesis Cartridge — 27C322 / 27C160 / 27C800 / FRAM Saves / Reset Switch

Cartridge PCB for **Sega Mega Drive / Sega Genesis** supporting several EPROM types and, optionally, non-volatile FRAM memory for game saves and RESET-based ROM bank switching.

The primary configuration uses a **27C322** EPROM. The PCB also supports **27C160** and **27C800** devices. The configuration is selected using SMD jumpers.

> **Important:** Carefully check the position of all jumpers before powering on the board. Incompatible or simultaneously installed jumpers may cause incorrect operation.

![Cartridge PCB](https://github.com/Viva76/Cart_FERAM/blob/main/Images/card.jpg)

## Features

- U4 EPROM:
  - **27C322** — 4 MB;
  - **27C160** — 2 MB;
  - **27C800** — 1 MB.
- ROM bank switching using the **RESET** signal, implemented with U3 **74HC393**. The address lines controlled by the counter are selected using jumpers **JP3, JP4, JP5**.
- Save-game support using U5 **F1808B FRAM**. ROM/FRAM chip-select logic is implemented using U2 **74HC74** and U1 **74HC139**.

---

# 1. EPROM Selection

The installed EPROM type is selected using jumpers **JP6** and **JP7**.

**JP6 and JP7 must never be installed at the same time!**

| EPROM | Capacity | JP6 (`≤2MB`) **"160 800"** | JP7 (`4MB`) **"322"** |
|---|---:|---|---|
| 27C800 | 1 MB | installed | open |
| 27C160 | 2 MB | installed | open |
| 27C322 | 4 MB | open | installed |

JP6 connects EPROM **pin 32** to +5 V and is marked **"160 800"** on the PCB.

JP7 connects EPROM **pin 32** to the **H20** line and is marked **"322"** on the PCB.

---

# 2. ROM Bank Switching Using RESET

The PCB can use the **RESET** signal to sequentially switch between ROM banks.

The bank-switching circuit is based on U3 **74HC393**. The counter outputs can be connected to the upper address lines of the ROM.

The following jumpers select the address lines controlled by the counter:

- **JP3** — address line **A20**;
- **JP4** — address line **A19**;
- **JP5** — address line **A18**.

Each jumper has two positions:

| Position | Function |
|---|---|
| **REG** (1–2) | Address line connected to the corresponding output of U3 74HC393 |
| **BUS** (2–3) | Address line connected directly to the address bus |

## Bank Configuration Table for a 4 MB EPROM

For U4 **27C322**, the number of available banks depends on how many upper address lines are controlled by U3 **74HC393**.

| JP3 (A20) | JP4 (A19) | JP5 (A18) | Number of Banks | Bank Size |
|---|---|---|---:|---:|
| BUS | BUS | BUS | 1 | 4 MB |
| REG | BUS | BUS | 2 | 2 MB |
| REG | REG | BUS | 4 | 1 MB |
| REG | REG | REG | 8 | 512 KB |

The page/bank switching sequence is determined by the **Q0–Q2** outputs of the U3 74HC393 counter.

> For 27C160 and 27C800, the actual available ROM capacity is determined by the installed device. The table above describes the full 4 MB configuration using a 27C322.

## If RESET-Based Bank Switching Is Not Required

1. **Do not install U3, R2, C1, or C4.**
2. Set **JP3, JP4, and JP5** to the **BUS (2–3)** position.

In this configuration, the EPROM address lines are connected directly to the address bus.

---

# 3. FRAM Save Memory

For games that support save data, the PCB provides non-volatile memory using U5 **F1808B FRAM**.

Memory chip selection using the **CE** signal is implemented using:

- U2 **74HC74**;
- U1 **74HC139**.

## JP1 — ROM Size Selection for Save-Enabled Games

JP1 **ROM SIZE** determines how one of the address signals is generated for the memory-selection logic:

| JP1 | Mode |
|---|---|
| **1–2** (`4MB`) | 4 MB game ROM |
| **2–3** (`≤2MB`) | 2 MB or smaller game ROM |

---

# 4. Configuration Without Save Memory

For a cartridge configuration without FRAM:

- U5 F1808B — **do not install**;
- U2 74HC74 / U1 74HC139, used for the FRAM selection logic — **do not install**;
- C5, C6, C7 — **do not install**;
- **JP2 `No FRAM` — install**;
- Configure the remaining jumpers according to the installed EPROM.

---

# 5. Configuration Examples

## 27C322, One 4MB Game, No Save Memory

| Component | Configuration |
|---|---|
| EPROM | 27C322 |
| JP6 **"160 800"** | open |
| JP7 **"322"** | installed |
| U3 74HC393 | do not install |
| JP3–JP5 | BUS |
| F1808B | do not install |
| U2 74HC74 | do not install |
| U1 74HC139 | do not install |
| JP2 **No FRAM** | installed |

## 27C322, One 4MB Game, With Save Memory

| Component | Configuration |
|---|---|
| EPROM | 27C322 |
| JP6 **"160 800"** | open |
| JP7 **"322"** | installed |
| U3 74HC393 | do not install |
| JP3–JP5 | BUS |
| U5 F1808B | install |
| U2 74HC74 | install |
| U1 74HC139 | install |
| JP1 **ROM SIZE** | 1–2 `4MB` |
| JP2 **No FRAM** | open |

## 27C322, Four 1MB Games, No Save Memory

| Component | Configuration |
|---|---|
| EPROM | 27C322 |
| JP6 **"160 800"** | open |
| JP7 **"322"** | installed |
| U3 74HC393 | install |
| JP3 (A20) | REG |
| JP4 (A19) | REG |
| JP5 (A18) | BUS |
| F1808B | do not install |
| U2 74HC74 | do not install |
| U1 74HC139 | do not install |
| JP2 **No FRAM** | installed |

## 27C160 / 27C800, One Game, With Save Memory

| Component | Configuration |
|---|---|
| EPROM | 27C800 / 27C160 |
| JP6 **"160 800"** | installed |
| JP7 **"322"** | open |
| U3 74HC393 | do not install |
| JP3–JP5 | BUS |
| U5 F1808B | install |
| U2 74HC74 | install |
| U1 74HC139 | do not install |
| JP1 **ROM SIZE** | 2–3 `≤2MB` |
| JP2 **No FRAM** | open |

---

# 6. Important Notes

### JP6 and JP7

**JP6 and JP7 are mutually exclusive!**

Both jumpers must **never** be installed at the same time.

- **JP6** — `≤2MB` mode;
- **JP7** — `4MB` mode.

### JP3–JP5

Only the following jumper positions are allowed:

- **1–2 (REG)** — address line controlled by U3 74HC393;
- **2–3 (BUS)** — address line connected directly to the bus.

**Combining both positions is not allowed!**

### C2 and C3

The C2 and C3 capacitor footprints support both:

- radial electrolytic capacitors, **CP_Radial_D5.0mm_P2.00mm**;
- SMD **1206** capacitors.

---

# 7. Project Files and Documentation

- 📄 **[Cartridge schematic — PDF](https://github.com/Viva76/Cart_FERAM/tree/main/docs/schematic.PDF)**
- 📋 **[Bill of Materials (BOM)](https://github.com/Viva76/Cart_FERAM/tree/main/docs/ibom.html)**
- 📦 **[Gerber files for PCB manufacturing](https://github.com/Viva76/Cart_FERAM/tree/main/Gerber/Cart_FeRAM-gerber.rar)**

![Cartridge PCB — front view](https://github.com/Viva76/Cart_FERAM/blob/main/Images/front.jpg)

![Cartridge PCB — back view](https://github.com/Viva76/Cart_FERAM/blob/main/Images/back.jpg)

---

## ⚠️ Disclaimer

*This project is intended for personal use. The author is not responsible for any damage to your hardware resulting from the use of this project. All modifications are performed at your own risk.*
