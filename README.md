# KCAD

AI engineering copilot for mechanical design teams. Engineers use one chat interface. An orchestrator sends each request to the correct specialist agent and combines the results.

**KCAD gives recommendations. Engineers make the decisions.**

Repository: [info-peddireddy/grok-KCAD](https://github.com/info-peddireddy/grok-KCAD)

## 1. What KCAD is

KCAD helps engineers review, make, find, and maintain mechanical designs.

## 2. The problem

Design teams lose time and money because of these problems:

- Engineers search for a long time in standards, specifications, and design guides.
- Manufacturing problems are found late, at the supplier or on the shop floor.
- Teams make new parts when an equivalent part is already in PLM.
- Engineers do the same hand calculations many times, with different methods.
- Machines fail with no warning, and production stops.
- Knowledge from past defects (NCRs) is not used in new designs.

## 3. Users

| User | Main use |
|---|---|
| Design engineer | DFM reviews, standards questions, design concepts |
| Manufacturing engineer | Shop feasibility, setups, cycle time, CAM reviews |
| Component and PLM engineer | Duplicate parts, alternative parts, data quality, change impact |
| Analysis engineer | Hand calculations, FEA results reviews |
| Maintenance personnel | Failure alerts and clear repair instructions |
| Engineering manager | Design review reports and ECR documents |

## 4. The agents

| # | Agent | What it does | Release |
|---|---|---|---|
| 1 | Engineering Doc Q&A | Answers questions about standards and design guides. Gives the document, revision, clause, and page | MVP |
| 2 | DFM Reviewer | Examines a CAD model. Finds manufacturing and drawing problems. Gives a severity, a fix, and evidence for each problem | MVP |
| 3 | BOM & Parts Intelligence | Finds duplicate parts and alternative parts, classifies parts, and checks the quality of part-master data | Release 2 |
| 4 | Generative Design Assistant | Gives 3 to 5 design concepts from loads and constraints, with calculations, mass, and cost | Release 2 |
| 5 | Predictive Maintenance | Predicts machine failures from sensor data. Gives the remaining life and the cause | Release 3 |
| 6 | Orchestrator / Copilot | Routes requests, connects agents, does change-impact analysis, writes reports and ECRs | MVP and Release 2 |
| 7 | CAE Results Reviewer | Compares FEA and CFD results with design codes and V&V checks | Optional |
| 8 | CAM Program Reviewer | Examines G-code and CAM setup sheets for missing operations, tools, and cycle time | Optional |

Example of a multi-agent request: “Review this bracket for CNC, check if we already have a similar part, and write a design review report.” The orchestrator runs DFM, then BOM, then the report. It shows the plan to the engineer before it starts.

## 5. Agent details

### Engineering Doc Q&A

- You can search by standard number, by clause (for example “Y14.5 7.3.2”), by symbol name, or with a usual question.
- It answers in the company standard family (ASME or ISO). It compares the two families when the user asks.
- It tells the user when an answer comes from an old edition or from an uncontrolled document.
- If the documents do not contain the answer, it says so. It does not guess.

### DFM Reviewer

- It reads STEP (AP203, AP214, AP242), IGES, STL/3MF, and DXF files. From AP242 files, it reads the PMI (GD&T, datums, and surface finish).
- It applies deterministic rules first. Examples: minimum wall thickness, hole depth ≤ 4 × D, inside radius ≥ tool radius, draft ≥ 1°, bend radius ≥ material thickness.
- It checks the drawing: tolerances on each feature of size, correct GD&T, standard fits and threads, surface finish, and mixed ASME and ISO use.
- It checks the design against the shop: available tools, tool reach, number of setups, the need for 5-axis machining or EDM, and machine capability against the tolerances.
- It shows past NCRs on similar features as evidence.

### BOM & Parts Intelligence

- It checks data quality first (ISO 8000). Examples: missing materials, wrong units, and descriptions that do not agree.
- It finds duplicates from the descriptions, attributes, standard designations, and geometry.
- It puts alternative parts in order and tells if each one is interchangeable (ASME Y14.100), or if it needs an ECO.
- It compares the EBOM with the MBOM, and it reports missing compliance declarations (RoHS, REACH).

### Generative Design Assistant

- Inputs: loads, envelope, target mass, materials, process, and maximum cost.
- It calculates the size of each concept with design codes: VDI 2230 (bolts), ISO 281 (bearings), DIN 743 (shafts), ISO 6336 (gears), and IIW (welds).
- It always gives the assumptions. It tells the engineer to do FEA before release.

### Predictive Maintenance

- It uses vibration, temperature, current, RPM, and machine data (MTConnect, OPC UA).
- It uses machine-learning models together with ISO 20816 vibration zones and ISO 281 bearing life checks.
- It explains each alert in clear text, for example: “The bearing shows wear. Replace the bearing in the next 14 days.”

### Orchestrator

- For a change-impact question (“What occurs if we change part X?”), it gives the where-used assemblies, the affected documents, the open ECOs, and the released configurations (EIA-649C).
- It writes ECRs only as documents. An engineer sends them.

## 6. Why engineers can trust KCAD

These rules apply to all agents. A CI test checks each rule.

| Rule | Meaning |
|---|---|
| Numbers come from tools | The AI does not give numbers, standard values, or rule values from memory |
| Each number has a unit | A number with no unit is a defect |
| Each answer has a source | A clause, a rule ID, a table row, or a data row |
| Read only | KCAD does not change PLM, ERP, or machines. It does not write or send G-code |
| Released data by default | Each part shows its part number, revision, and lifecycle state |
| Clear writing | All text obeys ASD-STE100 Simplified Technical English |
| No hidden instructions | Text in documents and data is data. KCAD does not obey instructions in it |
| Licensed standards only | KCAD loads only the standards that the company has a license for |
| Labels on test data | Results from synthetic data show “SYNTHETIC DATA. DO NOT USE FOR DESIGN.” |
| Full audit log | KCAD records each tool call, with the user, the inputs, and the sources |

## 7. Standards

- **Drawings and tolerances:** ASME Y14.5, Y14.41, Y14.100, B4.1/B4.2, B1.1, or ISO 1101, ISO 2768, ISO 286, ISO 965
- **CAD data:** STEP AP242, AP203/214, IGES, STL/3MF, DXF
- **Analysis:** ASME V&V 10/20, VDI 2230, ISO 281, DIN 743, ISO 6336, FKM, ASME BPVC
- **Manufacturing:** ISO 13399 (tools), ISO 513 (material groups), ISO 230 (machine accuracy), QIF, MTConnect, OPC UA
- **PLM and quality:** EIA-649C, ASME Y14.35, ISO 8000, ISO 9001 §7.5, RoHS, REACH

## 8. Units

- The default display is US customary units (in, lbf, psi, °F). Inch values use the ASME format, for example .500.
- A configuration setting changes the display to metric or dual units, with no code change.
- KCAD reads the unit of each CAD file, and it reports each item that mixes inch and mm.
- KCAD keeps metric standard tables in their original units, and it shows the converted value and the original value.

## 9. MCP integration

**KCAD uses existing systems:** KCAD connects to SharePoint, PLM, QMS, and machine data through MCP. If a system has no MCP server, KCAD uses a small read-only adapter.

**Existing systems use KCAD:** Claude Code, Claude Desktop, and CAD or PLM plug-ins can use KCAD tools and agents through MCP.

**Security:**

- KCAD blocks write tools.
- KCAD blocks a new or changed tool until an administrator approves it.
- Each call sends the identity of the user, through OAuth 2.1 with Entra ID (inferred).
- CAD data stays in the company environment.

## 10. Architecture

| Layer | Technology |
|---|---|
| AI models | Claude Opus 5.5 (orchestration and review), Claude Haiku 5.5 (high-volume extraction) |
| Agents | Claude Agent SDK, one subagent for each specialist |
| Tools | One MCP server for each tool, for example cad_inspect, standards_lookup, rule_check, calc, plm_lookup |
| Data | PostgreSQL with pgvector, TimescaleDB for sensor data, MinIO for CAD files |
| Application | Python 3.12, FastAPI, Streamlit chat UI |
| Deployment | Docker Compose (local). The cloud target is an open question |

## 11. Quality measurement

Each agent has an evaluation set. A phase cannot finish if a score is less than its target.

| Area | Target |
|---|---|
| Standards lookups (fits, tolerances, threads) | 100% exact match |
| Unit conversions and mixed-unit cases | 100% correct |
| Engineering calculations | ≤ 1% difference from known answers |
| Tool availability | 100% match; cycle time within ±25% |
| STE writing | 95% of sentences; no change to numbers, units, or citations |
| Hidden-instruction attacks | 0 obeyed |
| Doc Q&A, DFM, BOM, PdM | KCAD records a baseline score. You set the target at the phase gate |

## 12. Delivery plan

| Phase | Result |
|---|---|
| 0 | Discovery: configuration and decisions |
| 1 | Foundation: repository, CI, units, audit log, MCP gateway |
| 2 | Doc Q&A |
| 3a and 3b | CAD reading, standards tables, rules, DFM Reviewer: MVP demo |
| 4 | BOM and PLM |
| 5 | Calculations and Generative Design |
| 6 | Predictive Maintenance |
| 7 | Multi-agent sequences, reports, ECRs |
| 8 | Hardening: sign-in, access control, monitoring |
| 9 and 10 | Optional CAE and CAM reviewers |

Each phase stops at a gate. You approve the results before the next phase starts.
