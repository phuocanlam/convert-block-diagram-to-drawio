---
name: hardware-block-diagram-generator
description: Convert hardware block diagram images, system architecture diagrams, or IC/FPGA pinout drawings into native draw.io XML code. Trigger when the user provides an image of a hardware block diagram or system architecture and requests to recreate, redraw, or export it to draw.io.
---

# Hardware Image to Draw.io XML Generator

A specialized skill to analyze and convert photos, scans, or bitmap images of hardware block diagrams and system architectures directly into native draw.io XML (`.drawio`) format with strict spatial alignment, interface isolation, and zero-overlap net routing.

---

## Core Layout & Routing Principles

### 1. Edge-Aligned Interface Sub-boxes
- Never lump interface names or pin labels into the center of a parent IC/symbol.
- Split every peripheral interface (such as I2C, SPI, PCIe, UART, GbE, USB, DDR4 MIG IP, GTH, PMIC, Sideband, etc.) into its own distinct sub-box (`mxCell` vertex).
- Snap sub-boxes directly along the four outer boundaries of the parent block (Left, Right, Top, Bottom) matching the physical pinout and signal direction shown in the source diagram.

### 2. Centered Parent Symbol Typography
- Place the primary component title (e.g., `RPi CM4/CM5`, `Kintex UltraScale FPGA`, `Host MCU`) strictly in the geometric **Center** of the main symbol frame.
- Keep the title area clear of internal sub-boxes, net lines, and routing labels.

### 3. Strict Net Routing & Bus Isolation (Zero Overlap)
- **Individual Net Lines:** Each sub-box must output an independent net. Do not bundle distinct signals into a single combined line unless explicitly shown as a consolidated bus in the reference image.
- **Parallel Orthogonal Routing:** Net lines running toward multi-pin connectors (e.g., B2B Connector, Backplane) or originating from discrete sources (e.g., OSC banks) must travel on separate, parallel horizontal tracks with designated vertical pitch (minimum 25–40 px spacing).
- Configure all connections with `edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;` and explicit routing waypoints (`<Array as="points">...<mxPoint x="..." y="..."/></Array>`) to enforce neat 90-degree corners without intersecting other components.

### 4. Directional Arrowhead Semantics
- **Unidirectional (`endArrow=block;`):** Clocks (CLK), PWM signals, Power Enable/Reset lines, and reference voltages (VREF).
- **Bidirectional (`startArrow=classic;endArrow=classic;`):** High-speed serial buses (PCIe, GbE, USB), memory buses (DDR4), and bidirectional control links (I2C, SPI, UART with flow control, Sideband).

### 5. Robust XML Syntax Protocol
- Set non-empty string values for all labels; use `value=" "` instead of `value=""` to prevent XML parsing halts (`AttValue: ' expected`).
- Wrap the complete output in a standard `<mxfile>...</mxfile>` container so users can import it directly via **Extras > Edit Diagram...** in draw.io.

### 6. Strict 1:1 Mapping & Zero Abstraction
- **No Grouping:** Never group, abstract, or summarize multiple identical components (e.g., individual DDR5 slots, separate MCIO ports, or discrete NVMe connectors) into a single block. 
- **Exhaustive Detail:** Every minor IC, switch, MUX, TPM, eMMC, flash memory, and peripheral port shown in the source image must be represented as a distinct `mxCell`. 
- **Fidelity over Brevity:** The generated XML must strictly reflect a 1:1 component and net count with the original diagram, regardless of the resulting file length. Do not simplify the architecture.

### 7. Strict Anti-Truncation & No Lazy Generation
- **No Shortcuts or Placeholders:** You must explicitly write out EVERY SINGLE `<mxCell>` for all components and EVERY SINGLE `<mxCell edge="1">` for all net connections shown in the image.
- **Zero Omissions:** NEVER use comments like `<!-- insert remaining lines here -->`, ellipses (`...`), or summarization blocks. 
- **Full Completion Requirement:** The XML output must contain 100% of the nodes and 100% of the routing lines present in the source architecture, regardless of how long the response becomes. Ignore standard length constraints.

---

## 4-Step Analysis & Conversion Pipeline

1. **Architectural Partitioning:**
   - **Center Axis:** Main processing blocks, SoCs, FPGAs, MCUs, or BMCs.
   - **Left Column:** Power supply units (PSUs), sensors, DRAM modules, clock oscillators (OSC), and local headers (PCIe Edge, JTAG).
   - **Right Column:** High-density board-to-board connectors (B2B), backplanes, or external I/O interfaces.

2. **Sub-box Inventory & Edge Mapping:**
   - Catalog all incoming and outgoing signal ports for each central component.
   - Calculate precise vertical/horizontal coordinates for sub-boxes to avoid label collisions (minimum 5–10 px margin between sub-boxes).

3. **Coordinate-Guided Net Routing:**
   - Trace each net from its source sub-box to its destination block.
   - Insert explicit orthogonal waypoints ensuring no lines pass behind or through intervening blocks.

4. **XML Code Assembly & Verification:**
   - Package geometry definitions, styles, and edge hierarchies into clean draw.io XML.
   - Verify that all vertex IDs and edge source/target references match without orphaned connections.

---

## User Import Instructions

1. Open [draw.io](https://app.diagrams.net/).
2. Navigate to **Extras** > **Edit Diagram...**
3. Select all existing text (`Ctrl + A`), clear the box, and paste the generated XML block.
4. Click **OK** to render the structured, editable block diagram.