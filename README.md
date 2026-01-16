# Concept Graph Text Editor

This project is a minimal browser-based tool to build and visualize concept graphs from a textual description. On the left you write a simple “mini language” describing concepts and relationships; on the right you see an automatically laid out graph with colored nodes, zoom and pan, and small badges indicating which concepts have definitions.

---

## 1. What this application does

- Lets you describe a **conceptual graph** in plain text:
  - Nodes (concepts) like `Building`, `Heating`, `HeatPump`.
  - Edges (relationships) like `uses`, `has`, `typeOf`, `sourceType`.
- Renders a **graph view** on the right:
  - Nodes as colored, auto-sized boxes.
  - Edges as lines with labels.
  - Automatic force-directed layout to reduce overlap.
  - Zoom (mouse wheel) and pan (drag) on the canvas.
- Supports **saving and loading** the text description as `.txt` files.
- Allows **concept definitions** using `def Name = ...` lines, and shows a tiny `i` badge inside nodes that have a definition.

---

## 2. How to run it

- Save the HTML file (e.g. `concept_maps.html`) on your computer.
- Open it in a modern browser (Chrome, Firefox, Edge, Safari).
- No server, build step, or installation is required.
- Everything runs locally in the browser using HTML, CSS, and vanilla JavaScript.

---

## 3. Text syntax

All input is written in the left-hand text area. There are three main patterns.

### 3.1 Edge lines (arrow syntax)

Format:

```text
SourceConcept -> TargetConcept : relationText
```
### 3.2 Examples:

```text
Building -> HotWater : uses
Non-Residential -> Building : typeOf
Fuel -> Heating : sourceType
HeatPump -> Heating : sourceType
```
  - ```SourceConcept``` and ```TargetConcept``` become nodes.
  - The text after ```:``` appears as the edge label.
  - When the relation contains the word type (e.g. typeOf, sourceType, classType), both concepts at the ends of that edge are treated as type concepts and colored light blue.

### 3.2 Edge lines (space-RELATION-space syntax)
You can also write:

```text
SourceConcept relation TargetConcept : edgeText
```
Example:

```text
Building property surface : has
Heating computes powerPerHour : consumes
```
Heuristic rules in this app:

  - The first token is treated as the source concept (Building, Heating).
  - The last token is treated as the target concept (surface, powerPerHour).
  - Tokens in the middle (property, computes) are interpreted as relationship type markers:
    - If the token before the last is "property" or "computes", the target concept is treated as an attribute-like concept (non-main) and colored light gray.

### 3.3 Concept definitions
Use lines that start with def to define concepts:

```text
def Building = A human-made structure providing shelter or space.
def Heating = Process that raises indoor temperature.
```
Rules:
- Lines starting with def do not create edges.
- Format: ```def <ConceptName> = <description>```.
- The concept name is taken literally (including spaces if you choose to use them).
- Any concept that has a definition is marked internally and drawn with a small circular “i” badge in the top-right corner of its node box.

> **Note:** def is a reserved keyword in this tool. Normal graph lines should not start with `def` unless defining a concept.


 ## 4. Visual encoding in the graph
The canvas on the right uses a simple set of visual rules.

### Node fill color
- Dark blue: “main” concepts (e.g. ```Building```, ```Heating```, ```HotWater```) that are not attributes and not type concepts.
- Light blue: concepts participating in a relation whose name contains ```type``` (e.g. ```typeOf```, ```sourceType```); these are treated as **type concepts**.
- Light gray: attribute-like concepts, especially targets of patterns like ```"property X"``` or ```"computes X"``` (e.g. ```surface```, ```age```, ```powerPerHour```).

### Node text color
- White on dark or light blue.
- Black on light gray.

### Definition badge
- If a concept has a ```def Name = ...``` line:
- A small white circle with an i appears in the top-right corner of its box.

### Edges
- Drawn as straight lines between node centers.
- Edge label shows:
  - The relation text (e.g. uses, typeOf, sourceType).
  - The optional text after :.

## 5. Interaction
### Left side (text editor)
 - Edit text freely in the textarea.
 - As you type or paste, the graph updates automatically.
 - Use ```def Name = ...``` lines to add or change concept definitions.

### Right side (graph canvas)

#### Zoom
- Use the mouse wheel over the canvas.
- Zoom is centered on the cursor position.

#### Pan
- Click and drag on the canvas to move the view.

#### Layout
- Every time the text changes, a simple force-directed layout runs for a short time to improve node placement.

## 6. Saving and loading
- The controls under the header provide basic file operations.

### Save
- Type a filename (e.g. ```my_graph.txt```) in the file name input.
- Click **Save**.
- A ```.txt``` file with the exact contents of the textarea is downloaded.

### Load
- Use the file input (“upload a text file to load it”).
- Select a ```.txt``` file created earlier or any compatible description.
- The textarea is replaced with the file content and the graph re-renders.
This is a purely client-side mechanism using the browser’s Blob and File APIs.

### 7. Example graph input
You can paste this example into the editor to see how the visualization behaves:

```text
Building -> HotWater : uses
Building property surface : has
Building property age : has
Building property geoLocalitation : has
Building property address : has
Building property type : has
Building property inhabitants : contains
Building computes hourlyPowerDemands : consumes

Building -> Insulation : has
Insulation property insulationLevel : has
Building -> CadastralParcel : containedIn

Equipment -> HotWater : produces
HotWater computes powerPerHour : needs
BathTub -> Equipment : typeOf

Heating -> Equipment : input
Heating computes powerPerHour : consumes
Fuel -> Heating : sourceType
Gas -> Heating : sourceType
HeatPump -> Heating : sourceType
CollectiveHeating -> Heating : sourceType

CadastralParcel -> Locality : belongsTo

Non-Residential -> Building : typeOf
Residential -> Building : typeOf
House -> Residential : typeOf
Apartment -> Residential : typeOf

def Building = A constructed facility that provides indoor space for people or activities.
def Heating = A system or process that supplies heat to indoor spaces in the building.
def HotWater = Water heated above ambient temperature for domestic or service uses.
def Insulation = Material or systems that limit heat transfer through the building envelope.
def Residential = Buildings primarily used for housing people.
def Non-Residential = Buildings primarily used for non-residential activities such as commerce or industry.
```
