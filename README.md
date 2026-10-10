# 🪐 Studento-x86: 16-Bit Assembly Student Information System

<div align="center">
  <img src="https://img.shields.io/badge/Architecture-16--Bit%20Intel%20x86-008080?style=for-the-badge&logo=intel" alt="x86 Intel Architecture Badge">
  <img src="https://img.shields.io/badge/Assembler-NASM%20v2.16-00599C?style=for-the-badge&logo=assembly" alt="NASM Compiler Badge">
  <img src="https://img.shields.io/badge/Environment-DOSBox%20v0.74--3-734F96?style=for-the-badge&logo=dosbox" alt="DOSBox Platform Badge">
  <img src="https://img.shields.io/badge/License-MIT-emerald?style=for-the-badge" alt="MIT License Badge">
</div>

---

## 🌊 Overview

**Studento-x86** is a high-performance, lightweight **Student Information System (SIS)** written completely in raw **16-bit x86 Intel Assembly** using **NASM** syntax. Designed to compile down into a bare-metal `.COM` executable binary, it runs inside legacy x86 environments or modern virtualization engines like **DOSBox**.

While modern desktop tracking software demands hundreds of megabytes of disk and RAM space, Studento-x86 implements an entire flat-file database framework within a statically allocated **500-Byte RAM Array Segment**. It showcases advanced register manipulation, low-overhead string interpolation, and modular software execution without relying on massive external operating system dependencies.

---

## 🕹️ System Architecture Control Console

The application evaluates software states continuously using keyboard interaction interrupts (`INT 21h`, Function `01h`). Supervisors manipulate data records through an interactive command loop:

```
📥 SYSTEM CONTROL MATRIX
 ├── Add Student      ───► Dynamically capture profile inputs & structure memory slots
 ├── View Ledger      ───► Print complete database tables with auto-aligned columns
 ├── Alter Status     ───► Mutate life-cycle states (1 = Active | 2 = Left | 3 = Done)
 ├── Fee Accounting   ───► Audit outstanding balance invoices and adjust settlement flags
 ├── Attn Tracker     ───► Increment binary counters (Present Days vs Total School Days)
 ├── Grade Analyzer   ───► Convert score buffers, compute ranges, and print alphabet grades
 ├── Medical Logs     ───► Log health exceptions and append a 12-byte clinical note
 └── Core Shutdown    ───► Break operational loop and safely yield system vector control
```

---

## 🧠 Database Memory Allocation Blueprint

Every student entry is granted a fixed footprint of **exactly 50 Bytes**. The base memory array block (`db_`) features a hard boundary constraint allowing a maximum of **10 concurrent profiles**.

```
+─────────────────────────────────────────────────────────────────────────────+

|                          50-BYTE MEMORY RECORD SEGMENT                      |
+──────────┬──────────────┬──────┬───────┬──────┬────────┬───┬───┬───┬─────┬───+

|  ID (3)  |  Name (15)   |Age(2)|Cls(5) |St(1) |Fee (5) |Pd |Pr |Tot|Mks(3|...|
+──────────┴──────────────┴──────┴───────┴──────┴────────┴───┴───┴───┴─────┴───+
^          ^              ^      ^       ^      ^        ^   ^   ^   ^     ^
0x00       0x03           0x12   0x14    0x19   0x1A     0x1F0x200x210x22  0x25
```

### 📋 Detailed Structure Offsets

| Memory Offset | Structural Token | Data Width | Format & Operational Scope |
| :--- | :--- | :--- | :--- |
| `+00` (0x00) | **STUDENT_ID** | 3 Bytes | String tracking unique index ID (ASCII characters) |
| `+03` (0x03) | **STUDENT_NAME** | 15 Bytes | Character array field (Automatically space-padded) |
| `+18` (0x12) | **AGE_VALUE** | 2 Bytes | Flat numerical age allocation characters (ASCII) |
| `+20` (0x14) | **CLASS_DESIGN** | 5 Bytes | Classroom identifier or course allocation code |
| `+25` (0x19) | **STATUS_FLAG** | 1 Byte | Hex State Register (`01` = Active, `02` = Left, `03` = Done) |
| `+26` (0x1A) | **FEE_BALANCE** | 5 Bytes | Financial valuation balance sheet string (ASCII) |
| `+31` (0x1F) | **SETTLED_FLAG** | 1 Byte | Boolean indicator (`00` = Outstanding, `01` = Paid) |
| `+32` (0x20) | **ATTEND_PRESENT**| 1 Byte | Checked-in day counter (Raw binary accumulator) |
| `+33` (0x21) | **ATTEND_TOTAL** | 1 Byte | Total school sessions logged (Raw binary accumulator) |
| `+34` (0x22) | **EXAM_MARKS** | 3 Bytes | Academic scorecard buffer entry range (`000`–`100`) |
| `+37` (0x25) | **ASSIST_FLAG** | 1 Byte | Special accommodation binary tag (`00` = No, `01` = Yes) |
| `+38` (0x26) | **MEDICAL_NOTE** | 12 Bytes | Freeform clinical health commentary string segment |

---

## 💻 Compilation & Deployment Manual

Follow these quick steps to assemble the code and deploy it directly within your emulation sandbox environment:

### ⚙️ Environmental Setup
1. Download the **NASM Assembler** compiler and expose it to your global environment system paths.
2. Download and configure an instance of the **DOSBox Emulator**.

### 🛠️ Execution Pipeline

```bash
# Step 1: Run your host system console and assemble source code into flat machine binary
nasm -f bin student_system.asm -o STUDENTO.COM

# Step 2: Boot up DOSBox and mount your workspace folder to begin execution
# Inside the DOSBox terminal shell typing environment, input:
Z:\> MOUNT C C:\YourAssemblyProjectFolder
Z:\> C:
C:\> STUDENTO.COM
```

---

## 🔬 Under the Hood: Low-Level Engine Mechanics

<details>
<summary><b>🛡️ Fixed Column String Padding Logic (<code>cpy</code> / <code>rs</code>)</b></summary>
<p>

Since dynamic modern heap-tracking string management layers don't exist in low-level assemblies, character values could bleed over into neighboring memory fields if untruncated. The helper routing engine `cpy` solves this by tracking the user's keystrokes for a `0x0D` (Carriage Return) character. Once found, it clips the capture loop short and shifts execution to inject empty white spaces (`0x20`) into all remaining bytes of that field block, keeping console data matrices uniform.
</p>
</details>

<details>
<summary><b>🔢 String-to-Number Conversion Routine (<code>s2n</code>)</b></summary>
<p>

To evaluate arithmetic criteria, character array tokens like `"0"`, `"9"`, and `"5"` must be parsed into matching numerical binaries. The custom `s2n` routine reads each index of the text field sequentially, isolates the raw element, subtracts character encoding offsets (`SUB byte, 48`), scale-shifts the mathematical value via decimal base multipliers (`10`), and aggregates the calculation total within the low accumulator register `AL`.
</p>
</details>

<details>
<summary><b>🎓 Evaluation Matrix Branch Tree (<code>pgr</code>)</b></summary>
<p>

Once values are computed and hosted safely within `AL`, conditional branching logic controls grading assignment by comparing the numeric values sequentially using jumps (`CMP` ──► `JGE`):
```
           [Is Score >= 90?] ─── YES ───► Print 'A' & Return
                   │ NO
           [Is Score >= 75?] ─── YES ───► Print 'B' & Return
                   │ NO
           [Is Score >= 60?] ─── YES ───► Print 'C' & Return
                   │ NO
           [Is Score >= 50?] ─── YES ───► Print 'D' & Return
                   │ NO
                   └────────────────────► Print 'F' & Return
```
</p>
</details>

<details>
<summary><b>🧼 Video Buffer Reset Control (<code>cls</code>)</b></summary>
<p>

To present clean UI window transitions instead of unmanaged scrolling terminal interfaces, screen updates execute raw Video BIOS Interruption requests (`INT 10h`) with instruction modes containing parameter properties `AX=0003h`. This system flush instantly purges old characters from the video address frame buffer and locks the cursor array index back to base coordinate index `(0,0)`.
</p>
</details>

---

## 📜 Educational Open-Source License

Distributed completely under the **MIT License**. This project is written purely for educational research, low-level binary register scheduling analysis, memory architecture experiments, and custom hardware interrupt integration modeling. Feel free to copy, modify, and expand this source framework for personal computer science assignments.