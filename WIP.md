# Idéer och WIP

Senast uppdaterad: 2026-08-30

Detta dokument är *source of truth* för projekt och idéer.  
Syftet är att hålla isär **aktivt arbete**, **väntande arbete**, **idéer** och **avslutade initiativ**, så att nya idéer inte automatiskt blir nya åtaganden.

## Aktivt WIP

| Projekt | Status | Nästa steg |
|---|---|---|
| Bayes / diagnostik | Aktivt | Fortsatta självstudier i Bayesiansk inferens och utveckling av konceptet för händelsebaserad, lärande fordonsdiagnostik. |
| Hemskola | Aktivt | Fortsätta prova och iterativt förbättra 30-minutersupplägget med barnens faktiska läromedel. |
| Arduino-projektet | Nästan klart | Mjukvaruuppdateringen är klar. Endast väderskydd återstår. |
| Badrumsrenovering | Behöver initieras | Skicka offertförfrågan och ordna första besök för prisuppskattning. |

## Väntar / blockerat

| Projekt | Status | Nästa steg / trigger |
|---|---|---|
| Matematikböcker för barn | Väntar på svar | Avvakta svar. Om svar uteblir: ring NCM. |

## Idébank

Idéer här är **inte åtaganden**. De får utforskas, men ska inte flyttas till aktivt WIP utan ett medvetet beslut.

### Kapslingsdesigner för 3D-printade apparatlådor

**Idé:** Ett verktyg som hjälper till att konstruera kapslingar från komponentlista/BOM och återanvänder så mycket befintlig open source som möjligt, exempelvis OpenSCAD och OrcaSlicer.

**Önskat flöde:**

Idé → användarfall → funktionella/icke-funktionella krav → komponentlista + användning → 2D-layout → mekaniska detaljer och väderskydd → OpenSCAD → testutskrifter → STL → OrcaSlicer → G-code → 3D-printer.

**Problem verktyget ska minska:**

- Hålla reda på exakta PCB-mått och monteringshål.
- Säkerställa plats för kontakter, stickproppar, kablar och böjradier.
- Föreslå hål, luckor, kabelgenomföringar och andra lösningar för väderskydd.
- Generera små provbitar för passform i stället för att skriva ut hela kapslingen.
- Hjälpa till med tätningsytor för exempelvis silikon och plexiglas.
- Generera borrmallar/jiggar så hål i plexiglas och kapsling linjerar.
- Utgå från verkligt tillgängliga skruvar, pluggar, gångjärnspinnar, inserts, kabelgenomföringar m.m. i stället för godtyckliga dimensioner.
- Generera eller komplettera en BOM med den mekaniska hårdvara konstruktionen kräver.

**Arkitekturprinciper:**

- OpenSCAD är ett outputformat, inte domänmodellen.
- Neutral strukturerad designmodell mellan GUI och CAD-generator.
- Återanvänd open-source-komponenter där det är möjligt.
- Separera geometriska, tillverkningsmässiga, monteringsmässiga och miljömässiga constraints.
- Walking skeleton och vertikala slices.
- Varje iteration ska ge konkret användarvärde.
- Börja med ett verkligt Feather-kapslingsfall snarare än en generell CAD-plattform.

## Avslutat / Won't do

| Projekt | Utfall |
|---|---|
| Lokala vattensystem | **Won't do.** Idén är nedlagd och ska inte räknas som aktiv eller parkerad idé. |

## WIP-princip

> Utforska idéer fritt, men gör dem inte automatiskt till åtaganden.

När en ny idé uppstår:

1. Lägg den i **Idébank**.
2. Utforska tillräckligt för att förstå värde och ungefärlig omfattning.
3. Parkera den.
4. Flytta den till **Aktivt WIP** endast genom ett explicit prioriteringsbeslut.
5. När något avslutas, flytta det till **Avslutat / Won't do** och frigör WIP innan nästa större initiativ startas.
