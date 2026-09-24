# IoB Bot Profile Ontology : Reproducibility Package

Companion repository for:

> "Internet of Bots: Toward a Bot Profile-Centric Ecosystem for Digital Services"  

---

## Contents

```
botprofile.owl          — OWL/RDF ontology (TBox + cancer care ABox, 6 bot instances)
sparql/
  CQ1.rq               — SPARQL query for Competency Question 1
  CQ2.rq               — SPARQL query for Competency Question 2
  CQ3.rq               — SPARQL query for Competency Question 3
  CQ4.rq               — SPARQL query for Competency Question 4
  results/
    CQ1_results.csv    — Expected result for CQ1
    CQ2_results.csv    — Expected result for CQ2
    CQ3_results.csv    — Expected result for CQ3
    CQ4_results.csv    — Expected result for CQ4
```

---

## Ontology

**IRI:** `http://www.IoB.org/ontologies/botprofile`  
**Format:** OWL 2 / RDF-XML  
**Tool used:** Protégé 5.5 with HermiT 1.4 reasoner

The ontology defines:
- **40 OWL classes** covering bot types (InformationalBot, TaskOrientedBot, MonitoringBot, CreativeBot), technology types (RuleBased, NarrowAI, FoundationModelAI, RetrievalBased), autonomy levels (FullyAutomated, PartiallyAutomated, Low), communication styles (HumanToBot/H2B, BotToBot/B2B, BotToService/B2S), interaction modes (Multimodal, CommandAndControl, Conversational, Graphical, …), domain ontology references, and communication protocols.
- **6 object properties:** `hasAutonomy`, `hasCommunicationStyle`, `hasInteraction`, `hasTechnology`, `ontologiesUsed`, `protocolsSupported`
- **7 data properties:** `name`, `botID`, `domain`, `capabilityLevel`, `authLevel`, `dataSensitivity`, `provider`
- **Note:** `authLevel` is modeled as a DatatypeProperty (xsd:string values: `"Internal"`, `"Restricted"`, `"Public"`) to match the SPARQL literal filters used in CQ2 and CQ4.
- **6 bot named individuals** (cancer care continuum ABox) + supporting enumeration individuals (26 total)

---

## HermiT Consistency Check

Performed in Protégé 5.5 via **Reasoner → HermiT 1.4 → Start reasoner**:

- No unsatisfiable classes detected
- All 6 bot individuals correctly inferred to belong to their asserted types (InformationalBot, TaskOrientedBot ×3, MonitoringBot, CreativeBot)
- Ontology is logically consistent

---

## SPARQL Competency Questions

Queries are expressed in SPARQL 1.1 and executed against `botprofile.owl` in **Protégé 5.5** (Window → SPARQL query tab) or any SPARQL 1.1 engine (e.g., Apache Jena ARQ).

### How to run in Protégé 5.5

1. Open `botprofile.owl` in Protégé 5.5
2. Start the HermiT reasoner (Reasoner menu → Start reasoner)
3. Open the SPARQL query panel (Window → Tabs → SPARQL query)
4. Paste the content of a `.rq` file and click Execute

### How to run with Apache Jena ARQ (command line)

```bash
sparql --data botprofile.owl --query sparql/CQ1.rq
sparql --data botprofile.owl --query sparql/CQ2.rq
sparql --data botprofile.owl --query sparql/CQ3.rq
sparql --data botprofile.owl --query sparql/CQ4.rq
```

---

## Query Results

### CQ1. Interoperable semantic core (HL7 FHIR ∩ SNOMED CT)

> Which bots declare alignment with both HL7 FHIR and SNOMED CT?

| name |
|------|
| PrimaryCareBot |
| TreatmentProgrammerBot |
| CareTrackerBot |
| RecordsManagerBot |

4 instances. Bot 2 (SpecialistCoordinationBot) and Bot 3 (HealthInsuranceBot) reference ICD-10 rather than SNOMED CT and are correctly excluded, reflecting their administrative and billing roles.

---

### CQ2. Trust-critical autonomous components (FullyAutomated ∩ Restricted)

> Which bots operate fully autonomously and require restricted authorization?

| name |
|------|
| SpecialistCoordinationBot |
| CareTrackerBot |
| RecordsManagerBot |

3 instances. These bots share the highest autonomy and the most restrictive access profile, reflecting the design principle that bots operating without human confirmation must carry the strongest authorization constraints.

---

### CQ3. Clinical entry points (HumanToBot ∩ (SNOMED CT ∪ NCI Thesaurus))

> Which bots support direct human-to-bot interaction and declare alignment with at least one oncology-specific terminology standard?

| name |
|------|
| PrimaryCareBot |
| TreatmentProgrammerBot |

2 instances. PrimaryCareBot is identified as the patient intake entry point; TreatmentProgrammerBot as the oncologist-facing interface. The four remaining bots operate exclusively in bot-to-bot or bot-to-service mode and are correctly excluded.

---

### CQ4. Deterministic compliance verification (RuleBased ∩ Internal ∩ dataSensitivity=true)

> Which bots apply rule-based reasoning to handle sensitive patient data under internal authorization?

| name | domain |
|------|--------|
| HealthInsuranceBot | e-health, health-insurance |

1 instance. Only HealthInsuranceBot satisfies all three constraints. The domain string confirms its dual scope (e-health + health-insurance), demonstrating that multi-domain bots are correctly represented.

---

## Bot Instances Summary

| Bot | Individual IRI | Class | Technology | Autonomy | Auth | Ontologies Used |
|-----|---------------|-------|-----------|----------|------|-----------------|
| Bot 1 | PrimaryCareBot | InformationalBot | NarrowAI | PartiallyAutomated | Internal | HL7 FHIR, SNOMED CT |
| Bot 2 | SpecialistCoordinationBot | TaskOrientedBot | FoundationModelAI | FullyAutomated | Restricted | HL7 FHIR, ICD-10 |
| Bot 3 | HealthInsuranceBot | TaskOrientedBot | RuleBased | PartiallyAutomated | Internal | HL7 FHIR, ICD-10 |
| Bot 4 | TreatmentProgrammerBot | TaskOrientedBot | NarrowAI | PartiallyAutomated | Internal | NCI Thesaurus, HL7 FHIR, SNOMED CT |
| Bot 5 | CareTrackerBot | MonitoringBot | NarrowAI | FullyAutomated | Restricted | HL7 FHIR, SNOMED CT |
| Bot 6 | RecordsManagerBot | CreativeBot | FoundationModelAI | FullyAutomated | Restricted | HL7 FHIR, ICD-10, NCI Thesaurus, SNOMED CT |
