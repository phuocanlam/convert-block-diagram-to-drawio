## Component Extension & Incremental Modification Instructions

When requesting to append new components, headers, or bridge ICs onto an existing draw.io XML diagram, formulate the prompt using the following structured template to enforce strict spatial alignment, port separation, and zero-overlap routing[cite: 1]:

---

### Prompt Template: Adding Discrete Peripheral & Header Blocks

```text
Based on the design and routing principles defined in skills.md, update the existing draw.io XML by adding the following components and connections without modifying or overlapping existing blocks[cite: 1]:

1. Component A (Bridge / Transceiver IC):
   - Label: "FTDI USB-to-UART" (or specific MPN, e.g., "FT232RNL").
   - Geometric placement: Center the block at coordinates [specify (x, y) or relative offset, e.g., right of BMC / near bottom-left margin].
   - Interface sub-boxes: Create a dedicated edge-aligned vertex for "UART0" on the right boundary edge of the parent symbol[cite: 1]. Do not place the interface text in the symbol center[cite: 1].

2. Component B (Connector / Header):
   - Label: "1x3 Pin Header" (or connector designator, e.g., "JUART1").
   - Geometric placement: Position strictly parallel to the right of Component A with a horizontal offset of at least 120-160 px.
   - Distinct pinout cells: Represent each individual pin as a discrete vertex:
     * Pin 1: TXD
     * Pin 2: RXD
     * Pin 3: GND
   - Enforce 1:1 pin representation; do not summarize the header into an unmapped solid block[cite: 1].

3. Net Routing & Signal Integrity:
   - Route independent orthogonal net lines from the "UART0" sub-box to each respective pin on the header (zero net bundling)[cite: 1].
   - Directional arrowheads:
     * TXD line: Use unidirectional "endArrow=block;" pointing from FTDI to Header Pin 1[cite: 1].
     * RXD line: Use unidirectional "endArrow=block;" pointing from Header Pin 2 to FTDI[cite: 1].
     * GND line: Use a solid passive line with "endArrow=none;startArrow=none;".
   - Routing constraints: Apply "edgeStyle=orthogonalEdgeStyle;rounded=0;html=1;" with explicit routing waypoints (<Array as="points">...</Array>) to maintain clean 90-degree corners and zero intersection with adjacent components[cite: 1].

4. Output Protocol:
   - Return the complete, fully valid XML code enclosed inside a single <mxfile>...</mxfile> container for seamless import via Extras > Edit Diagram...[cite: 1]
   - Strictly write out every single <mxCell> without truncation, ellipses, or placeholder comments[cite: 1].