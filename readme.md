# Workshop Migration Assistance

Dit is een experimentele repository om te testen met:

- Agents
- Skills
- Instructions

Het doel is om een praktische playground te hebben waarin we kunnen valideren hoe een agent:

- context uit de repository leest,
- migratie-input begrijpt,
- analyses uitvoert,
- en bruikbare rapportage oplevert.

## Scope van deze repository

De focus ligt op migratie-assistentie rond data- en ETL-artefacten, met inputbestanden onder [migrations/input](migrations/input). Denk aan SQL, SSIS en begeleidende documentatie.

## Waarom deze repository

Deze repo is bedoeld om snel iteraties te doen op:

- prompt/instruction kwaliteit,
- skill-gedrag,
- agent-gedrag,
- en outputkwaliteit van analyses.

## Huidige status

Dit is een beginopzet. De basisstructuur staat, en de volgende stap is het verder uitbouwen van een analyse-agent die inputbestanden leest, samenvat en een rapport genereert op basis van wat we willen weten.

## Structuur (globaal)

- [migrations/input](migrations/input): broninput voor analyse
- [migrations/scenarios](migrations/scenarios): scenario-uitwerking
- [migrations/governance](migrations/governance): governance en werkwijze

## Richting voor uitbreiding

Vervolguitbreidingen die hierop passen:

- automatische intake van alle bestanden in de inputmap,
- type-specifieke analyse (SQL, SSIS, documentatie),
- standaard rapporttemplate met bevindingen, risico's en aanbevelingen,
- en betere traceerbaarheid van bron naar advies.
