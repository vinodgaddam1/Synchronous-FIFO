# Synchronous-FIFO

## 📌 1. Project Title
✅ **Single-Clock (Synchronous) FIFO Buffer — Parameterized Depth & Width**

---

## 📝 2. Description
This project implements a **synchronous FIFO (First-In First-Out) buffer** in Verilog, where **both read and write operations share the same clock (`clk`)** ⏱️.

It supports controlled access using:
- 🎛️ `cs` (chip select)
- ✍️ `wr_en` (write enable)
- 📖 `rd_en` (read enable)

and provides status flags:
- 🚫 `full`
- 🈳 `empty`

The FIFO uses internal memory plus read/write pointers to maintain ordering.

---

## ⭐ 3. Key Features
- ⏱️ **Single-clock FIFO** (read/write on same `clk`)
- ⚙️ **Parameterized**
  - `FIFO_DEPTH` (default 8)
  - `DATA_WIDTH` (default 32)
  - `FIFO_DEPTH_LOG = $clog2(FIFO_DEPTH)`
- 🎛️ `cs` gating support
- 🚦 `full` and `empty` flag generation
- 🔄 Reset initializes pointers and output

---

## 🔌 4. Module Interface

### 📄 Module Declaration
```verilog
module FIFO(clk,rst,cs,wr_en,rd_en,data_in,data_out,full,empty);
```

### 🧾 Ports
| Signal     | Dir | Width | Description |
|-----------|-----|-------|-------------|
| `clk`     | In  | 1     | ⏱️ System clock |
| `rst`     | In  | 1     | 🔄 Reset |
| `cs`      | In  | 1     | 🎛️ Chip select |
| `wr_en`   | In  | 1     | ✍️ Write enable |
| `rd_en`   | In  | 1     | 📖 Read enable |
| `data_in` | In  | `DATA_WIDTH` | 📨 Write data |
| `data_out`| Out | `DATA_WIDTH` | 📤 Read data |
| `full`    | Out | 1     | 🚫 FIFO full indicator |
| `empty`   | Out | 1     | 🈳 FIFO empty indicator |

---

## 🧠 5. Functional Behavior

### 🔄 Reset
On `posedge clk` when `rst = 1`:
- `write_pointer <= 0`
- `read_pointer <= 0`
- `data_out <= 0`

---

### ✍️ Write Operation
On `posedge clk`, if:
- `cs && wr_en && !full` ✅

Then:
- `mem[write_pointer] <= data_in`
- `write_pointer <= write_pointer + 1`

---

### 📖 Read Operation
On `posedge clk`, if:
- `cs && rd_en && !empty` ✅

Then:
- `data_out <= mem[read_pointer]`
- `read_pointer <= read_pointer + 1`

---

## 🚦 6. Full & Empty Conditions

### 🈳 Empty
FIFO is empty when:
```verilog
empty = (read_pointer == write_pointer);
```

### 🚫 Full
FIFO is full when the read pointer equals the write pointer with inverted MSB (wrap-around detection):
```verilog
full = (read_pointer == {~write_pointer[FIFO_DEPTH_LOG], write_pointer[FIFO_DEPTH_LOG-1:0]});
```
✅ This technique distinguishes **full vs empty** even when lower address bits match.

---

## ⚙️ 7. Parameter Example
To create a FIFO with **16 entries** and **8-bit data**:
```verilog
FIFO #(
  .FIFO_DEPTH(16),
  .DATA_WIDTH(8)
) u_fifo (
  .clk(clk),
  .rst(rst),
  .cs(cs),
  .wr_en(wr_en),
  .rd_en(rd_en),
  .data_in(data_in),
  .data_out(data_out),
  .full(full),
  .empty(empty)
);
```

---

## 📁 8. Files
- 📄 `FIFO.v` — Synchronous FIFO module

