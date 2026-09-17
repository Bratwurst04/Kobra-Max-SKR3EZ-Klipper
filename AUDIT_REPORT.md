# Teknisk och dokumentationsmässig audit

**Kobra-Max-SKR3EZ-Klipper — fas 1 med godkänd fas 2-uppföljning**

Fas 1-baslinjen och fyndens ursprungliga evidens är bevarade. Statuskolumnen och [fas 2-uppföljningen](#phase-2-status-and-validation) redovisar enbart de uttryckligen godkända ändringarna på `docs/audit-phase-2-2026-09-17`. Inga funktionella printervärden har ändrats.

## Audit scope

| Fält | Granskad baslinje |
|---|---|
| Repository | [Bratwurst04/Kobra-Max-SKR3EZ-Klipper](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper) |
| Branch vid hämtning | `main` |
| Commit SHA | [`0a5059781347635a45b5196a254ad758e8c844a7`](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/commit/0a5059781347635a45b5196a254ad758e8c844a7) |
| Commitdatum | 2026-09-17 15:46:25 CEST / 13:46:25 UTC |
| Lokal audit-branch | `audit/2026-09-17`, skapad från ovanstående commit; inte pushad |
| Auditdatum | 2026-09-17, baslinjeinventering 15:55 CEST; avslut 16:11 CEST |
| Leverans i fas 1 | Auditrapport utan ändringar i befintlig README, dokumentation eller cfg. Fas 2 redovisas separat nedan |

Den aktuella Git-checkouten är enda baslinjen. Den bifogade `Kobra-Max-SKR3EZ-Klipper-main(5).zip` innehåller samma 20 filer och samma innehåll som baslinjen. Arbetskopians textfiler har Windows-radslut; normaliserat till LF är de identiska med ZIP-filerna. Bild och STL-filer är byte-identiska. Äldre chattar, ZIP-filer och commits har inte hämtats eller använts för att återställa uppgifter. Det inklistrade underlaget används som uppdragsbeskrivning; påståenden inne i repot behandlas som granskningsmaterial.

Samtliga sju Markdown-filer och nio cfg-filer lästes i sin helhet. Därefter jämfördes uppgifter mellan filer, mot bilden och mot relevanta primärkällor. Fil- och radreferenser nedan länkar till den låsta committen, inte till en framtida version av `main`.

### Lästa och inspekterade filer

| Fil | Rader / kontroll |
|---|---|
| [README.md](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md) | 166, fullständig läsning |
| [Useful_links.md](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/Useful_links.md) | 45, fullständig läsning |
| [Hardware.md](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md) | 716, fullständig läsning |
| [Wiring.md](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md) | 528, fullständig läsning |
| [Journey.md](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md) | 1227, fullständig läsning |
| [Issues.md](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md) | 842, fullständig läsning |
| [config.md](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md) | 626, fullständig läsning |
| [printer.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/printer.cfg) | 37, fullständig läsning och statisk parsning |
| [steppers.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/steppers.cfg) | 98, fullständig läsning och statisk parsning |
| [tmc.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/tmc.cfg) | 30, fullständig läsning och statisk parsning |
| [bed.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/bed.cfg) | 17, fullständig läsning och statisk parsning |
| [fans.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/fans.cfg) | 19, fullständig läsning och statisk parsning |
| [leviq_probe.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/leviq_probe.cfg) | 55, fullständig läsning och statisk parsning |
| [accessories.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/accessories.cfg) | 6, fullständig läsning och statisk parsning |
| [macros.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/macros.cfg) | 57, fullständig läsning och statisk parsning |
| [KAMP_Settings.cfg](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/KAMP_Settings.cfg) | 36, fullständig läsning; villkorliga externa includes granskade |
| `docs/pics/Anycubic_Kobra_Max_Klipper.png` | Visuell granskning av hela kopplingstabellen |
| `docs/stl/Kobra Max SKR3 Adapter plate.stl` | Existens, exakt filnamn, versalisering och länkar |
| `docs/stl/Kobra max head 2.stl` | Samma kontroll |
| `docs/stl/Kobra max head 2 infill.stl` | Samma kontroll |

STL-filerna har inte mekaniskt passningskontrollerats eller provutskrivits. Endast en bild finns i repot; omnämnda fotografier, loggar, produkt-PDF:er, tidigare `config(1).zip` och resonans-CSV:er är inte medföljande bevismaterial i denna baslinje.

### Verktyg och kontrollnivå

- Git: hämtning av aktuell branch, commitidentifiering, lokal audit-branch och kontroll av oförändrade källfiler.
- PowerShell, `rg` och Python: fullständig filinventering, ZIP-jämförelse, radreferenser samt sökning efter pins, värden, statusord, historiska avgränsningar och beroenden.
- Lokal länkkontroll: 122 relativa fil-/bild-/ankarreferenser, exakt versalisering, Markdown-tabellernas kolumner, code fences och balansen i `<details>`/`<summary>`.
- Upstreams `ConfigFileReader` och `ConfigWrapper`: parsning av publicerad cfg och isolerad kontroll av obligatoriskt probe-offset. `ConfigAutoSave` användes för att pröva PID-konflikten utan att spara någon konfiguration.
- Statisk kontroll av aktiva sektioner, pinanvändning, makroreferenser och geometri. Alla nio cfg-filer klarade enskild syntaxparsning och fem publicerade Jinja-mallar kompilerades. Full include-laddning kräver fortfarande de saknade förutsättningarna. Ingen full Klippy-start, MCU-simulering eller hårdvarutest görs anspråk på.
- Primärkällor via webb och officiell källkod; 16 unika externa länkar i repot kontrollerades även med HTTP. HTTP 200 skiljs från verifierad sididentitet.

Klipper-källa för de lokala statiska kontrollerna: `72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2`. KAMP-källa för den villkorliga purgekontrollen: `997838cb36447d697da780aedf74d97dc1763e1e`. Dessa är auditens jämförelseversioner, **inte fastställda installerade versioner på skrivaren**. Den historiska MCU-versionen i [Issues.md:605–611](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L605-L611) anger inte hela den slutliga host-/extensionmiljön.

Ingen skrivare kontaktades, ingen G-code skickades och ingen fysisk åtgärd utfördes. Renderade makrokommandon användes endast som lokal text för analys.

## Executive summary

| Severity | Antal |
|---|---:|
| CRITICAL | 0 |
| HIGH | 1 |
| MEDIUM | 4 |
| LOW | 3 |
| INFO | 5 |
| **Totalt** | **13** |

**Repots centrala fakta är i huvudsak internt konsekventa.** Granskningen hittar ingen bevisad konflikt i aktiva MCU-pinnar, ingen felidentifiering av huvudkort/MCU och inget stöd för att de dokumenterade maskinspecifika LeviQ-signalerna ska bytas. Senare återöppnade LeviQ- och nätverksfel är tydligt åtskilda från tidigare fungerande tester. Input Shaper, nozzle cleaner och live-konfigurationsändringar är huvudsakligen korrekt åtskilda från den äldre publicerade cfg-snapshoten.

Det viktigaste nya tekniska fyndet är att den beskrivna PID-kalibreringen med `SAVE_CONFIG` saknar en förutsättning när PID-definitionerna ligger i inkluderade filer. Detta ger en reproducerad save-konflikt. README:s ordval ”working configuration snapshot” är också för starkt: repot dokumenterar självt att snapshoten saknar startförutsättningar.

Temperaturtexten behöver precisera att `max_temp` också tillåter motsvarande börvärden; den skapar ingen separat lägre driftgräns. Det är en begränsning att tydliggöra, inte bevis för att maskinen har överhettats eller att ett visst nytt gränsvärde är rätt. En villkorlig KAMP-kantgeometri behöver kontrolleras mot den faktiskt installerade versionen. Därför rekommenderas ingen automatisk ändring av funktionella cfg-värden.

INFO-posterna är verifieringsgränser eller villkorliga risker. De ska inte läsas som fem fastställda fel på skrivaren. Befintliga och redan tydligt dokumenterade snapshotbegränsningar räknas samlat som INFO, inte som nya dolda fel.

## Findings

**Säkerhet i tabellen betyder evidensstyrka**, inte elsäkerhetsklass. HIGH prioriteras före MEDIUM osv. Beskrivningar, radreferenser och severity avser fas 1-baslinjen. Status avser de godkända åtgärderna i fas 2; FIXED för A01/A03 betyder dokumentationsrättning, inte ändrad heater-konfiguration.

| ID / Severity | Status fas 2 | Fil | Avsnitt/rad | Problem | Motsägande källa eller evidensgräns | Rekommenderad ändring | Säkerhet |
|---|---|---|---|---|---|---|---|
| <a id="a01"></a>A01 **HIGH** | FIXED | [config.md:198–220](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L198-L220), [steppers.cfg:88–93](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/steppers.cfg#L88-L93), [bed.cfg:8–14](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/bed.cfg#L8-L14) | Generated calibration data; PID-definitioner | Guiden säger att kalibrera och använda `SAVE_CONFIG`, men båda heater-definitionerna har `control` och PID-tal i inkluderade filer. Upstreams save-kontroll avvisar dessa inkluderade värden. | Isolerat reproducerat för både `extruder` och `heater_bed`; [Klipper ConfigAutoSave](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/configfile.py) och [PID_CALIBRATE](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/extras/pid_calibrate.py). | Dokumentera hanteringen av inkluderade PID-värden: uppdatera dem manuellt, eller ordna en konsekvent SAVE_CONFIG-struktur. Flytta/kommentera inte fungerande heater-värden automatiskt under auditen. | Hög; faktisk statisk reproduktion, ingen heater körd. |
| <a id="a02"></a>A02 **MEDIUM** | FIXED | [README.md:155–163](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L155-L163) | How-to guide, särskilt 162 | ”Working configuration snapshot” beskriver den distribuerade uppsättningen för starkt. | [config.md:33–53](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L33-L53) säger uttryckligen att den inte är självförsörjande/körbar direkt: saknad `mainsail.cfg`, serial-placeholder, saknat `z_offset`, KAMP avstängt men anropat. | Skriv ”machine-specific reference snapshot” och länka direkt till förutsättningarna. Behåll historiska uppgifter om fungerande live-installation. | Hög; ordvalskonflikt, inte bevis för att maskinen aldrig fungerat. |
| <a id="a03"></a>A03 **MEDIUM** | FIXED | [config.md:306–316](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L306-L316), [config.md:365–374](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L365-L374), [steppers.cfg:95–97](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/steppers.cfg#L95-L97), [bed.cfg:16–17](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/bed.cfg#L16-L17) | Temperaturgränser | 280/100 °C beskrivs som säkerhets-/felgränser, samtidigt som kommentarerna anger Anycubic 260/90. Avsikten ”inte måltemperatur” är rimlig, men den lägre driftgränsen upprätthålls inte separat i cfg. | [Klipper Heater.set_temp](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/extras/heaters.py) tillåter börvärden upp till `max_temp`; ingen lägre spärr finns i publicerad startmacro. Slutlig hotend/sensor är inte identifierad, [Hardware.md:515–532](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L515-L532). | Förklara skillnaden mellan avsedd normal drift, tillåtna börvärden och skyddsgräns. Dokumentera komponenternas verifierade gränser innan eventuella numeriska ändringar beslutas. | Hög för semantiken; faktisk säker temperatur för slutlig hårdvara okänd. |
| <a id="a04"></a>A04 **MEDIUM** | FIXED | [config.md:281–296](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L281-L296), [Hardware.md:456–469](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L456-L469) | Extruderkalibrering / Current verified features | PA 0.44 introduceras som kalibrerat för ”current” extruder, trots att efterföljande Bowden/hotend/sensorändringar kräver ny kalibrering. | [config.md:78–87](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L78-L87), [README.md:66](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L66), [Journey.md:1068](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1068), [Journey.md:1095–1103](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1095-L1103) avgränsar både 0.44 och senare ~0.8 från slutlig aktuell kalibrering. | Ange ”earlier calibrated setup retained in the published snapshot”. Behåll siffrorna och länka till senare retuning-status. | Hög för tvetydigt tidsläge; inget bevis för att 0.44 måste ersättas med 0.8. |
| <a id="a05"></a>A05 **MEDIUM** | FIXED | [Hardware.md:319–329](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L319-L329), [Wiring.md:480–492](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L480-L492), [Journey.md:1007–1017](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1007-L1017), [Issues.md:642–710](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L642-L710), [config.md:70–74](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L70-L74) | USB/ADXL-förlopp | Kabeltopologi, USB-ID:n, Katapult-fel, hot-plug-fel och återhämtning upprepas i detalj över fem dokument. Ingen sifferkonflikt finns nu, men underhållsansvaret är utspritt. | Dokumentens egna roller anges i [Journey.md:856–877](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L856-L877); samma diagnostik återberättas i flera roller. | Låt Wiring äga kabeltopologin, Hardware komponentidentiteten, Issues detaljerade fel/loggar, Journey kronologin och config.md snapshotens beroenden. Korta övriga återberättelser och länka. | Hög för duplicering; risk för framtida drift, inte dagens tekniska fel. |
| <a id="a06"></a>A06 **LOW** | FIXED | [Issues.md:28](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L28), [Issues.md:432–433](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L432-L433) | Quick index → pause/cancel | Länken `#pause-or-cancel-could-crash-after-skipped-xy-steps` saknar motsvarande rubrik/explicit id. Målet är endast `<summary><strong>…</strong></summary>`. | Källans HTML innehåller inget ankare för den texten. Övriga lokala länkar klarade kontrollen. | Ge målsektionen en riktig rubrik eller ett explicit id som matchar länken. | Hög. |
| <a id="a07"></a>A07 **LOW** | FIXED | [Wiring.md:404](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L404), [fans.cfg:13–18](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/fans.cfg#L13-L18) | Hotend-fläktens startvillkor | Text/kommentar anger endast temperatur över 50 °C. En heater_fan går även när heaterns börvärde är skilt från noll, före uppnådd tröskel. | [Officiell heater_fan-dokumentation](https://www.klipper3d.org/Config_Reference.html#heater_fan) och [implementation](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/extras/heater_fan.py). | Beskriv båda villkoren. Ändra endast text/kommentar, inte fläktstyrningen. | Hög; inga felaktiga fan-pinnar. |
| <a id="a08"></a>A08 **LOW** | PARTIALLY FIXED | [Wiring.md:69](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L69), [Wiring.md:250](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L250), [Wiring.md:442](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L442), [Wiring.md:528](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L528), [Hardware.md:42](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L42), [config.md:164](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L164), [config.md:196](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L196), [config.md:230](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L230) | Rubrikhierarki och små språkfel | Flera dokument har många toppnivårubriker och blandar rubriknivåer i summaries. Exempel på språkfel: `motor.Connect`, `A (older version)`, `structurize`. | 17 H1 i Hardware, 22 i Journey, 8 i Issues, 4 i config.md och 3 i Wiring. Ingen obalanserad HTML eller trasig tabell hittades. | Små copy edits; normalisera vid behov till en dokumenttitel och underordnade avsnitt, med bevarade fungerande ankare. Ingen omskrivning av stilskäl behövs. | Hög för observationerna; layoutprioritet är redaktionell. |
| <a id="a09"></a>A09 **INFO** | ACCEPTED AS-IS / INFO | [KAMP_Settings.cfg:25–29](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/KAMP_Settings.cfg#L25-L29), [steppers.cfg:18](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/steppers.cfg#L18), [config.md:493–502](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L493-L502) | KAMP-purge vid högra kanten | Med auditens upstream-KAMP och repo-inställningarna genererar ett objekt inom X400–405/Y200–205 en avslutande purgeförflyttning till X425, över `position_max: 415`. | [KAMP Line_Purge](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging/blob/997838cb36447d697da780aedf74d97dc1763e1e/Configuration/Line_Purge.cfg) begränsar inte denna förflyttning mot axelmax. Installerad KAMP-version saknas; include är avstängt i snapshoten. | Kontrollera verklig KAMP-version och tillåtna objektplaceringar; dokumentera villkoret. Ingen ändring av maxtravel eller purgeprofil utan verifiering. | Hög för det statiska motexemplet; okänd tillämplighet på live-maskinen. |
| <a id="a10"></a>A10 **INFO** | ACCEPTED AS-IS / INFO | [config.md:33–53](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L33-L53), [printer.cfg:1–19](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/printer.cfg#L1-L19), [leviq_probe.cfg:12](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/leviq_probe.cfg#L12), [macros.cfg:31–38](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/macros.cfg#L31-L38) | Redan dokumenterade snapshotbegränsningar | Den publicerade cfg-uppsättningen kan inte användas fristående: host-include, verklig serial och effektivt probe-offset saknas; externa KAMP-kommandon är odefinierade. | Parsning stoppar på `mainsail.cfg`; isolerad fortsatt kontroll visar saknat `z_offset`. Detta står redan korrekt i config.md. | Behåll begränsningstabellen och exponera den tydligare från README, A02. Fyll inte i maskinunika värden från äldre material. | Hög; avsiktlig referenssnapshot, inte ny okänd maskinregression. |
| <a id="a11"></a>A11 **INFO** | ACCEPTED AS-IS / INFO | [Hardware.md:278–329](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L278-L329), [Wiring.md:79–102](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L79-L102), [Wiring.md:163–184](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L163-L184), [Wiring.md:480–492](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L480-L492) | Fysisk reproducerbarhet | Slutlig 5 V-/VUSB-arkitektur, vissa jumper-/switchlägen och exakt nivåomvandlare saknar dokumenterad slutstatus. | [BTT:s gränssnittsbeskrivning](https://global.bttwiki.com/SKR%203%20EZ.html#interface-introduction) beskriver val för VUSB, DIAG, matning och termistoringångar. Repo har testresultat men ingen komplett fysisk setupbild. | Komplettera med faktiskt verifierade lägen/modell när de finns. Behåll det som öppen fråga; frånvaron bevisar varken backmatning, fel pull-up eller USB-felorsak. | Hög för informationsluckan; verkligt elektriskt tillstånd ej avgjort. |
| <a id="a12"></a>A12 **INFO** | ACCEPTED AS-IS / INFO | [Useful_links.md:3–6](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/Useful_links.md#L3-L6), [Useful_links.md:43–45](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/Useful_links.md#L43-L45), [Hardware.md:600–609](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L600-L609), [config.md:64](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L64), [config.md:543](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L543) | Källproveniens och saknade originalbilagor | Flera fakta bygger på återgivna chattar, foton, PDF:er och loggar som inte medföljer. Att varje Useful_links-källa faktiskt användes under bygget kan inte oberoende spåras i baslinjen. | Författarens uppgift är underlag, men aktuell repo-struktur innehåller bara en bild och inga originaltestloggar/listing-PDF:er. | Märk oberoende verifiering som ej möjlig; lägg vid framtida behov till daterade källreferenser som faktiskt finns. Tillför inte audittens nyfunna källor till byggens historik. | Hög för evidensgränsen; ingen anklagelse om felaktig historik. |
| <a id="a13"></a>A13 **INFO** | FIXED | [Journey.md:1041–1045](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1041-L1045), [config.md:344](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L344) | Höga accelerationstal | 90 000 mm/s² beskrivs som uppnått och som belägg för stor mekanisk/motormarginal. Underlaget i repot visar rapporterade inställningar/observationer, inte uppmätt rörelseprofil. | Ingen komplett live-cfg, G-code eller rörelselog finns; [config.md:481](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L481) och [Issues.md:200](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L200) skiljer redan kommenderad och faktisk Z-rörelse åt. | Skriv ”requested/set acceleration” och begränsa slutsatsen till ingen observerad step loss i just testet. Behåll historiska siffror. | Hög för underlagets begränsning; faktisk uppnådd acceleration ej fastställd. |

## Cross-file consistency matrix

Kolumnerna använder filernas radnummer i den granskade committen. **R** = README, **H** = Hardware, **W** = Wiring, **J** = Journey, **I** = Issues, **C** = config.md. ”—” betyder att faktumet inte dokumenteras där; det är inte i sig en brist.

| Fact | README | Hardware | Wiring | Journey | Issues | config.md | cfg | Result |
|---|---|---|---|---|---|---|---|---|
| Original Kobra Max, SKR 3 EZ v1.0, H723 | 15–18 | 21–25, 119–146 | 79–102 | 82–86, 270–294 | 597–611 | 150–155 | steppers 1–2; printer serial-placeholder | Konsekvent; H743 är historiskt antagande |
| Fyra EZ2209 v1.1, ett Z-driverparallellsystem | 18, 81 | 179–202, 238–256 | 83–90, 295–335 | 179–201 | 309–339 | 321–353 | fyra TMC-sektioner; endast stepper_z | Konsekvent; Z_TILT är inte tillgängligt med detta upplägg |
| Pi Zero 2 W, USB till MCU | 19 | 268–329 | 478–492 | 223–231, 1007 | 605–611, 642–679 | 70, 93–95 | MCU serial utan verkligt id | Konsekvent modell; slutlig matningsarkitektur öppen |
| X/Y endstops PC1/PC3 | 58 | 216–232 | 91–92, 237, 285 | 460–476 | — | 262–263 | steppers 15, 35 | Konsekvent inklusive inverterade/pull-up-ingångar |
| E0 step/dir/enable/UART | — | 181–200 | 89–90 | 417–435 | 414–428 | 353 | PD15/PD14/PC7/PC6 | Konsekvent; historiskt E1-fel är rättat |
| SCL=PB15 probe, SDA=PB14 reset, LEVE=PE5 LED | 30–31, 59, 64 | 410–414 | 100–102, 125–129 | 544–605 | 61–70 | 417–430, 510–514 | probe 2, 8; accessories 4 | Konsekvent; etiketterna är inte en aktiv I²C-buss |
| LeviQ nivåomvandling 5↔3.3 V | 32 | 417–427 | 163–184 | 48 | 61 | pinroller | inga elektriska matningar definieras i cfg | Ingen dokumenterad kopplingskonflikt |
| Hotend HE0/PB3, TH0/PA2 | 61 | 493–532 | 94–95, 144–147 | 478–487 | 485–523 | 265–266, 82–87 | steppers 84–86 | Pins stämmer; slutligt sensorfabrikat ej identifierat |
| Bed HB/PD7 styr extern MOSFET; TB/PA1 | 25, 62 | 568–609 | 96–97, 378–390 | 502–518 | 529–555 | 360–374 | bed 3–6 | Konsekvent; PSU-märkeffekt förväxlas inte med bedström |
| Fans PB7/PB6 | 63 | 554–562 | 98–99, 400–404 | 493–500 | — | 389–403 | fans 8, 15 | Pins stämmer; A07 gäller startbeskrivning |
| Bowden, ratio 3:1, RD22.350 | 22 | 454–469 | E0M 249–257 | 721–734 | 578 | 78, 283–296 | steppers 73–74 | Konsekvent; ej universell kalibrering |
| PA0.44 → senare ~0.8 → ny kalibrering väntar | 66, 133 | 456–469 | kalibreringsvarning 515 | 736–748, 1068, 1103 | 523, 578 | 78–87 kontra 283 | steppers 79=0.44 | Avsiktlig historik, men ”current” behöver förtydligas A04 |
| Mekaniskt X0–415/Y0–438; mesh/print X14–405/Y23–423 | — | 65–80 | 515 | 803–819 | — | 184–189, 493–500 | steppers 17–18/37–38; probe 44–45 | Konsekvent och maskinspecifikt; villkorlig purgefråga A09 |
| Safe home 200,200; senare ~208,223 | — | geometri 65–80 | — | 1083–1087 | testpunkter 116–122 | 47, 449–463 | probe 33=200,200 | Avsiktlig snapshot/live-skillnad |
| Probe två medianprover, 5 mm retract, 0.05 tolerans, tre retries | — | initial reset 433–444 | 199–206 | historik 583–591, 917–985 | initialt 63–78; senare diagnostik | 437–489 | probe 17–21 | Konsekvent; senare SAMPLES=1 och 2 mm/s är separata tester |
| Reset LOW100/HIGH+600 ms | — | hänvisningar 443–446 | 199–206 | resetmilsten | 65–68 | 426–433, 478 | probe 27–30 | Konsekvent snapshot; senare 200/700 inte automatisk ersättare |
| EPCOS aktiv kurva efter Generic3950-försök | 23, 61, 133 | 528–532 | funktionell termistorverifiering 132–133 | 1095–1103 | 485–523 | 48, 82–87 | steppers 86 | Konsekvent programvaruval; inte fysisk komponentidentifiering |
| PID finns i vanliga sektioner | retuning 61 | 505–513, 589–594 | kalibreringsvarning 515 | 712–719 | 527, 555 | 42, 212, 378–386 | steppers 90–93; bed 11–14 | Korrekt beskrivet att värden finns; save-förutsättning saknas A01 |
| KAMP fungerade på maskinen, är avstängt i snapshoten | 26, 65 | meshroll | 206 | 797–835 | 246 | 39–40, 570–596 | printer 13 kommenterad; macros anropar | Avsiktlig skillnad, korrekt i C; README-ordval A02 |
| Mellow FLY ADXL345/RP2040 och VBESTLIFE-hubb | 67, 139 | 36–37, 319–329 | 478–492 | 1005–1017 | 636–710 | 55–72 | ADXL-sektioner saknas | Konsekvent; duplicated diagnostics A05 |
| MZV X59.2/Y26.6 Hz; sparad live-cfg saknas | 67 | 36, 694 | kalibreringshänvisning 492 | 1013–1017, 1140–1145 | sensorväg 673–679 | 59–68 | input_shaper saknas | Ingen motsägelse mellan utförd analys och saknad cfg |
| Runout PC2, inte funktionellt verifierad | 70, 138 | 471–487 | 93, 239–240, 419 | 1105–1112 | — | 49, 516 | sensorsektion saknas | Konsekvent pending |
| BBL cleaner X~34–76/Y431, Z2 endast approach | 68, 140 | 637–641 | — | 1019–1021 | — | 45, 74 | NOZZLE_WIPE saknas | Konsekvent initial drift; inte verifierad kontakthöjd |
| Originaldisplay används inte; KlipperScreen optional | 116–122, 141 | 103–113 | 414 | 55 | — | — | ingen displaydrivrutin | Konsekvent; display_status är inte fysisk displaykonfiguration |
| Monteringsplatta och två head-STL | 83–109 | 151–164, 366–369, 645–649 | 371 | — | coverhistorik 266–284 | — | — | Alla tre filer och länkars versalisering stämmer |

### Full aktiv pinjämförelse

Följande uppsättning är lika i cfg och Wiring:s aktuella tabeller. BTT:s officiella pinout bekräftar SKR-sidan. `!` och `^` redovisar Klipper-funktion och ändrar inte vilken MCU-pin som används.

| Funktion | Step / dir / enable eller signal | UART | Fysisk SKR-anslutning |
|---|---|---|---|
| X | PD4 / !PD3 / !PD6 | PD5 | XM |
| Y | PA15 / PA8 / !PD1 | PD0 | YM |
| Z | PE2 / PE3 / !PE0 | PE1 | ZAM + ZBM, samma driver |
| Extruder | PD15 / PD14 / !PC7 | PC6 | E0M |
| X/Y endstop | ^!PC1 / ^!PC3 | — | X-STOP / Y-STOP |
| Hotend heater / termistor | PB3 / PA2 | — | HE0 / TH0 |
| Bed control / termistor | PD7 / PA1 | — | HB till extern MOSFET / TB |
| Part cooling / hotend fan | PB7 / PB6 | — | FAN0 / FAN1, separata switched returns |
| Probe / reset | ^!PB15 / PB14 | — | WiFi-header via nivåomvandlare |
| LED | PE5 | — | Servo GPIO |
| Runout, endast mappad | PC2 | — | E0-DET; inte aktiv i cfg |

Det finns 27 unika aktiva fysiska pins och referensen `probe:z_virtual_endstop`. Ingen dubbel aktiv fysisk pinanvändning hittades. Extern `mainsail.cfg` och faktisk live-konfiguration ingår inte i detta påstående. Anycubics egna signalroller och en omvänd Z-coil verifieras av repots maskinberättelse; BTT:s tabell kan inte bevisa just dessa fysiska ledningar.

## Current-status audit

Tabellen anger **sista stödda status i repot**, inte en ny funktionstestning. Daterade och odaterade grenar har inte pressats in i en påhittad gemensam tidsordning.

| Funktion | Samlad aktuell status | Stöd |
|---|---|---|
| Motion och X/Y endstops | Grundläggande X/Y/Z/E-rörelse och X/Y-homing verifierade. Aggressiva senare XY-tester gav step loss; ingen allmän höghastighetsgaranti. | [README.md:56–58](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L56-L58), [Issues.md:333–339](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L333-L339), [Issues.md:385–388](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L385-L388), [Journey.md:1041–1045](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1041-L1045) |
| Bedvärme / extern MOSFET | Behållen och verifierad i bygget; inget senare specifikt bedfel dokumenterat. | [README.md:62](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L62), [Hardware.md:568–609](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L568-L609), [Issues.md:552–555](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L552-L555) |
| Hotend och termistor | Flera byten; slutlig modell okänd. EPCOS-kurva återställd efter sämre Generic3950-försök. PID/flow/max-flow/PA behöver slutlig omkalibrering. | [README.md:61](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L61), [Hardware.md:515–532](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L515-L532), [Issues.md:485–523](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L485-L523) |
| LeviQ och Z-homing | Elektriska roller fastställda. Initiala homing/mesh-milstolpar står kvar; senare triggerförskjutningar och homingproblem är återöppnade. En tät varm punktserie avslutar inte frågan. | [README.md:59–60](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L59-L60), [Issues.md:225–247](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L225-L247), [Journey.md:1157–1159](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1157-L1159) |
| Bed mesh / KAMP | Tidigare integration och purge fungerade. Ingen slutligt tillförlitlig hel startsekvens efter LeviQ-utredningen belagd. Publicerad KAMP-include är avstängd. | [README.md:65](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L65), [config.md:39–40](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L39-L40), [config.md:576](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L576), [Journey.md:1133–1134](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1133-L1134) |
| Host / nätverk | USB-MCU-kommunikation och tillfälliga återhämtningar belagda; återkommande åtkomst-/LAN/Wi-Fi-fel ej avslutade. | [README.md:69](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L69), [Hardware.md:292–308](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L292-L308), [Issues.md:791–838](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L791-L838) |
| USB-hubb / accelerometer / Input Shaper | FLY-sensorfråga och X/Y-analys lyckades. USB-enumerationsfel återhämtat via power cycle; slutlig cfg, slutlig Y-montering och kontrollerad före/efter-utskrift saknas. | [Issues.md:636–710](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L636-L710), [config.md:55–74](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L55-L74), [Journey.md:1005–1017](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1005-L1017) |
| Filament runout | PC2 mappad. Närvarokontroll/startvillkor/pause föreslagna, ännu inte återrapporterat funktionstestade. | [README.md:70](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L70), [Hardware.md:471–487](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L471-L487), [config.md:516](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L516), [Journey.md:1112](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Journey.md#L1112) |
| Nozzle cleaner | Monterad på bäddmarginal och initial funktion rapporterad. Slutligt makro, kontakthöjd och full startintegration saknas. | [README.md:68](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L68), [Hardware.md:637–641](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L637-L641), [config.md:74](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L74) |
| Extrusion / PA / flöde | Historisk RD22.350/3:1 och PA0.44. Senare ~0.8 hör till ett annat tillstånd. Intermittent grinding/skipping och slutlig retuning är öppna. 22 mm³/s är inte verifierad kontinuerlig kapacitet. | [README.md:57](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L57), [README.md:66](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L66), [Issues.md:564–586](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Issues.md#L564-L586), [config.md:78–87](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L78-L87) |
| Fans / LED / display | Toolhead-fans och LED verifierade. Originaldisplay används inte. Elektronik-/PSU-fläktarnas detaljer är fortfarande ofullständigt dokumenterade. | [README.md:63–64](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/README.md#L63-L64), [Hardware.md:550–562](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Hardware.md#L550-L562), [Wiring.md:398–419](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L398-L419) |

## Configuration/documentation mismatches

### Reproducerad PID-save-konflikt — A01

En läsande reproduktion använde Klippers egen configläsare. Den saknade hostfilen `mainsail.cfg` hoppades över **endast i testharnessen**, inte i repositoryfilerna. Därefter byggdes de fyra fält som PID-kalibreringen vill spara (`control`, `pid_kp`, `pid_ki`, `pid_kd`) och kördes genom upstreams konfliktkontroll. Båda heaters gav motsvarande konflikt med inkluderat `control`-värde. Även inkluderade PID-fält måste hanteras; att bara flytta `control` löser inte hela strukturen.

Detta är en kontrollerad reproduktion av förutsättningen för att spara, inte en faktisk kalibrering eller körning av `SAVE_CONFIG` mot skrivaren. [config.md:214–220](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L214-L220) behöver beskriva hur inkluderade kalibreringsvärden förvaltas. Det är möjligt att den verkliga live-installationen redan hade en annan filstruktur; den är inte med här.

### Vad snapshoten kan och inte kan göra — A02/A10

| Del | Statisk observation | Bedömning |
|---|---|---|
| Aktiva includes | Sju lokala funktions-cfg finns; `mainsail.cfg` saknas | Faktiskt parse-stopp som levererat; korrekt omnämnt i config.md |
| MCU | Placeholder `/dev/serial/by-id/usb-Klipper_stm32h.........` | Avsiktligt maskinunikt värde att ersätta |
| Probe | `#z_offset: 0` är kommentar; inget SAVE_CONFIG-block ger värdet | Obligatoriskt effektivt värde saknas; korrekt omnämnt |
| KAMP | `printer.cfg` kommenterar bort include; tre aktiva include-rader inne i KAMP_Settings pekar på extern ej medföljande `KAMP/` | Dessa saknade filer är villkorliga beroenden, inte aktiva parsefel före aktivering |
| PRINT_START | `SMART_PARK` och `LINE_PURGE` saknar definition i den aktiva publicerade uppsättningen | KAMP kan inte behandlas som en fristående valfri include utan motsvarande startmacro-hantering |
| Standard mesh | `BED_MESH_CALIBRATE` finns via `[bed_mesh]` även utan KAMP | Utan extern wrapper blir detta vanlig mesh, inte automatiskt KAMP-adaptiv mesh |
| Cancel/pause | `on_error_gcode: CANCEL_PRINT`, `[pause_resume]`, host-include | Kräver kompatibel hostkonfiguration; den saknade filens innehåll kan inte granskas |
| Output-pinnar | `leviq_reset` och `nozzle_led` definieras och används med samma namn | Ingen saknad intern SET_PIN-definition |
| G-code/Jinja | PRINT_START och LED-blinksekvens kan extraheras av Klippers parser | Ingen påvisad parserkonflikt från tabs/kommentarer; inte full runtimevalidering |
| Dubbla sektioner | Inga dubbla aktiva sektionsdeklarationer i de medföljande aktiva filerna hittades | Extern Mainsail kan innehålla avsiktliga överlagringar; ej bedömt som dubbelkonfigurationsfel utan filen |
| Dockbara probe-makron | `Attach_Probe`/`Dock_Probe` nämns som KAMP-variabler, men `probe_dock_enable: False` | Ingen aktiv saknad dockningsfunktion |
| TMC Autotune | Include kommenterad; fil saknas | Korrekt dokumenterad optional/historisk funktion |

Ingen av dessa luckor ger mandat att återinföra ett gammalt Z-offset, MCU-id, ADXL-cfg, NOZZLE_WIPE eller PID-tal.

Två ytterligare integrationsvillkor är värda att behålla i dokumentationen utan att kalla dem konstaterade fel: `M104 S150` väntar inte på stabil nozzletemperatur före probing (redan förklarat i [config.md:539–545](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/config/config.md#L539-L545)), och `M83` i startmakrot förutsätter att slicerns efterföljande extrusion använder rätt läge. Någon slutlig slicerprofil finns inte här. Ingen av observationerna bevisar orsaken till LeviQ-felen eller ett faktiskt felaktigt utskriftsförlopp.

### Temperatur och fläkt — A03/A07

`max_temp: 280` respektive `100` är verkliga Klipper-gränser. En etikett i dokumentationen skapar ingen separat börvärdesgräns på 260/90. Exempelvis ligger ett önskat börvärde 270/95 inom de publicerade programvarugränserna; detta är en semantisk illustration, **inte ett rekommenderat temperaturtest**. Repot måste fortsatt skilja mjukvarugräns från den faktiska komponentens verifierade temperaturtålighet.

Hotend-fläktens implementation är rimlig. Den startar även när heaterns måltemperatur aktiveras medan sensorn ännu är under 50 °C. Texten bör inte få denna normala funktion att framstå som ett fel.

### Geometri och villkorlig KAMP-kant — A09

Aktiva X/Y-rörelsegränser innehåller både hela meshrektangeln och det publicerade safe-home-läget. Probe X/Y-offset är 0/0. Senare cleanerkoordinaten Y431 ligger inom mekanisk Y438 men utanför print-/meshgränsen Y423, vilket stämmer med beskrivningen som underhållsområde. Z2 beskrivs redan korrekt som approach med frigång, inte kontakt.

För ett hypotetiskt objekt med polygon `[(400,200),(405,200),(405,205),(400,205)]`, helt inom dokumenterad printyta, ger auditens externa KAMP-version och publicerade `purge_amount: 25`/`purge_margin: 8` följande geometri:

| Del | Beräknat värde |
|---|---:|
| Purge-start X | `(400+405)/2 - 25/2 = 390` |
| Purge Y | `200 - 8 = 192` |
| Purge-slut X | `390 + 25 = 415` |
| Efterföljande förflyttning för att bryta strängen | `415 + 10 = 425` |
| Publicerat X-max | 415 |

Den sista förflyttningen är alltså 10 mm utanför konfigurerad travel. Vid korrekt kända koordinater väntas Klipper avvisa en sådan rörelse som utanför räckvidd; beräkningen bevisar inte en fysisk krasch. Installerad KAMP-revision och verkliga slicerobjekt saknas. KAMP är dessutom inaktivt i den publicerade snapshoten. Fyndet förblir därför INFO/needs verification och kan inte användas som bevis för att redan rapporterade lyckade purger var felaktiga.

### Skillnader som redan är korrekt dokumenterade

- Input Shaper X59.2/Y26.6 och dess smoothing-gränser är analysresultat; cfg saknar shapersektioner. `max_accel: 3000` är snapshotens värde, inte ett påstående om en senare sparad 1800-inställning.
- Safe home 200,200 och senare ~208,223 är olika tillstånd. Kommentarens ”Mitt på bedden” i cfg är en ungefärlig äldre beskrivning, inte skäl att ändra koordinaten under auditen.
- Tidiga och sena TMC-strömmar, interpolation, microsteps samt 100/600 kontra 200/700 ms reset är avgränsade mellan snapshot och tester.
- Tidigare lyckad PID, varm punktserie och ny warm Z-offset i en senare gren behöver inte ha samma datum eller hardwaretillstånd. Frånvaron av fullständiga loggar får inte fyllas med en påhittad ordningsföljd.
- EPCOS-kurvans bättre manuella extrusion efter Generic3950 är ett dokumenterat funktionellt resultat, inte säker fysisk termistoridentifiering.

## Broken links / Markdown issues

### Interna länkar och struktur

122 relativa referenser kontrollerades, inklusive avkodade mellanslag i STL-namn, filnamnens exakta versalisering och rubrikankare. **Ett fel hittades:** A06 i Issues-indexet. Inga referenser till saknade lokala mål eller case-only-fel hittades. Omnämnda historiska filnamn utan länk, såsom temporära kalibrerings-CSV:er, är inte trasiga länkar.

Code fences var balanserade. `<details>` och `<summary>` var balanserade utan detekterat felaktigt avslutsdjup. Markdown-tabellerna hade konsekventa kolumnantal. Detaljnavigering, många H1-rubriker och mindre språkfel är A08; ingen full webbläsarbaserad visuell layoutcertifiering görs anspråk på.

Den inbäddade kopplingsbilden har äldre `Strain Gauge i2C`, `5V?`, PC0/optiskt Z och ZR/ZL-anteckningar. [Wiring.md:18–35](https://github.com/Bratwurst04/Kobra-Max-SKR3EZ-Klipper/blob/0a5059781347635a45b5196a254ad758e8c844a7/docs/Wiring.md#L18-L35) säger redan att texttabellerna är nyare och att dessa etiketter är historiska/osäkra. Därför räknas de inte som en ny aktuell elektrisk motsägelse. En framtida tydlig ”historisk bild”-märkning inne i själva PNG:n kan hjälpa vid separat delning, men bilden har inte redigerats.

### Externa länkar

Det finns 16 unika externa URL:er i de sju Markdown-filerna. 15 gav HTTP 200 i direktkontrollen. Raspberry Pi gav HTTP 403 där men var läsbar via webbkällan. Inga säkert döda externa länkar har fastställts. Ett JavaScript-skal med 200 räcker inte för att bevisa att ett visst modellobjekt finns kvar.

| Länk | Resultat och beskrivningskontroll |
|---|---|
| [Klipper Config Reference, probe](https://www.klipper3d.org/Config_Reference.html#probe) | 200; rätt officiell referens och relevant avsnitt |
| [Thingiverse 5380573](https://www.thingiverse.com/thing:5380573) | 200 med generiskt klientskal; webbindex identifierar Kobra Max/Vyper printhead/gantry quiet cover, i linje med beskrivningen. Modellgeometri ej verifierad |
| [NetworkManager.conf](https://networkmanager.dev/docs/api/latest/NetworkManager.conf.html) | 200; relevant officiell managed/ifupdown-definition |
| [Debian ip-neighbour](https://manpages.debian.org/trixie/iproute2/ip-neighbour.8.en.html) | 200; rätt manualsida och STALE-definition |
| [Raspberry Pi power warnings](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#power-supply-warnings) | Direkt 403, webbverktyget kunde läsa rätt dokument/avsnitt. Åtkomstskillnad, inte konstaterad trasig länk |
| [NIIMBOT D110](https://niimbots.com/products/d110-portable-wireless-connect-rechargeable-mini-label-printer-with-tape?variant=43704982470892) | 200; sidtitel identifierar rätt produkt. Att just en äldre variant användes kan bara stödjas av byggberättelsen |
| [Thingiverse 6247028](https://www.thingiverse.com/thing:6247028) | 200, men endast generiskt klientskal; inget tillräckligt läsbart objektnamn. Adressen svarar, specifik adapteridentitet förblir okontrollerad |
| [SWIO Adapter Kit](https://github.com/smartwareio/SWIO-Kobra-Max-Adapter-Kit) | 200; rätt konverterings-/adapterprojekt. Andra maskinens pinout används inte som bevis för denna maskin |
| [fuegovic backup](https://github.com/fuegovic/kobra-max-klipper-backup) | 200; titel anger Kobra Max Klipper setup/config. Tidigare webb-cachemiss var inte en 404 |
| [ryanpie86 conversion](https://github.com/ryanpie86/Anycubic-Kobra-Max-Klipper-Conversion) | 200; rätt konverteringsprojekt |
| [invisiblek kobraplus](https://github.com/invisiblek/klipper_config/tree/kobraplus) | 200; rätt konfigurationsrepo/branch; korrekt beskrivet som närliggande maskin |
| [SmartwareIO kit](https://www.smartwareio.com/product/kobra-max-mainboard-and-print-head-kit/9) | 200; sidtitel matchar Kobra Max Mainboard and Print Head Kit. Ingen oberoende testning av kitet |
| [iFixit SKR3 EZ](https://www.ifixit.com/Guide/4.+Installing+Klipper+on+SKR3+EZ/152185) | 200; korrekt guide. Den visar H743-inställning och får inte ersätta detta repos verifierade H723-val; repo har redan denna varning |
| [Thingiverse 5420549](https://www.thingiverse.com/thing:5420549) | 200 med klientskal; webbindexets titel stämmer med Thebeatbandits Kobra Max Direct Drive Mount. Inte bevis på att den monterats här |
| [Reddit: Klipper package](https://www.reddit.com/r/anycubic/comments/12a2fqp/any_interest_in_a_kobra_max_klipper_package/) | 200 och läsbar indexerad tråd med matchande titel; endast länkrelevans kontrollerad |
| [Reddit: working config](https://www.reddit.com/r/anycubic/comments/ynvbj0/anycubic_kobra_working_config/) | 200 och läsbar indexerad tråd med matchande titel; beskrivs korrekt som allmän Kobra-diskussion |

Nuvarande text om vilka källor som användes under bygget har inte ändrats eller kompletterats med påhittade datum. Auditens primärkällor nedan är kontrollkällor för denna audit, inte tillägg till den historiska byggprocessen.

## External verification

Följande tekniska kontroller har faktiskt gjorts mot primärkällor. De externa jämförelseprojekten och Reddit-trådarna ovan användes endast för länk-/relevanskontroll.

| Verifierat påstående | Primärkälla | Vad kontrollen stöder och inte stöder |
|---|---|---|
| SKR step/dir/enable/UART, endstops, heater/termistor/fan och PB14/PB15/PE5 | [BTT officiell pinouttabell](https://github.com/bigtreetech/SKR-3#skr-3--skr-3-ez-pinout-table) | SKR-sidans anslutningar stämmer med pinmatrisen. Ingen oberoende verifiering av Anycubics proprietära harness |
| Kortets valbara matnings-/gränssnittslägen | [BTT SKR 3 EZ wiki](https://global.bttwiki.com/SKR%203%20EZ.html#interface-introduction) | Förklarar varför fysisk VUSB/jumperstatus påverkar reproducerbarhet. Bevisar inget felaktigt läge på användarens kort |
| H723, 128 KiB bootloader och 25 MHz är rimlig firmwarekombination | [Klipper officiellt SKR3-exempel](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/config/generic-bigtreetech-skr-3.cfg) | Exemplet stöder H723/H743-varianter. Maskinens riktningar, termistorkurva och geometri kopieras inte från exempelkonfigurationen |
| Probe z_offset är obligatoriskt | [Klipper probe-reference](https://www.klipper3d.org/Config_Reference.html#probe) | Bekräftar det redan dokumenterade saknade effektiva värdet |
| PID-save begränsas av inkluderade fält | [ConfigAutoSave](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/configfile.py), [pid_calibrate.py](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/extras/pid_calibrate.py) | Stöder A01 och lokal konfliktkontroll; ingen faktisk heaterkalibrering |
| max_temp begränsar även börvärdet | [Heater.set_temp](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/extras/heaters.py) | Stöder A03. Säger inget om faktisk temperaturtålighet hos okänd hotend |
| Heater_fan kan gå innan sensorn når tröskeln | [Klipper heater_fan](https://www.klipper3d.org/Config_Reference.html#heater_fan), [implementation](https://github.com/Klipper3d/klipper/blob/72b3cdb4e4dce4b65b6cd3751963dad0bbc713e2/klippy/extras/heater_fan.py) | Stöder A07; befintlig cfg-funktion behöver inte ändras |
| KAMP-komponenter/objektinformation och purgealgoritm | [KAMP officiellt repo](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging), [Line_Purge vid auditrevision](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging/blob/997838cb36447d697da780aedf74d97dc1763e1e/Configuration/Line_Purge.cfg) | Stöder externa beroenden och villkorliga kantberäkningen. Installerad revision okänd |
| Moonraker objektbearbetning är en hostförutsättning | [Moonraker file_manager](https://moonraker.readthedocs.io/en/latest/configuration/#file_manager) | `enable_object_processing` är relevant; ingen moonraker.conf finns att verifiera lokalt |
| FLY USB ADXL använder RP2040/egen MCU-konfiguration | [Mellow introduktion](https://mellow.klipper.cn/en/docs/ProductDoc/ToolBoard/fly-usb-adxl/fly-usb-adxl/), [Mellow konfigurationsexempel](https://mellow-3d.github.io/fly_adxl345_usb_klipper_config.html) | Stöder arkitekturen. Slutlig serial, faktisk montageorientering och live-adxl.cfg är inte verifierade |
| NetworkManager ifupdown managed=false är inte global WLAN-avstängning | [NetworkManager.conf](https://networkmanager.dev/docs/api/latest/NetworkManager.conf.html) | Repots rättelse i Issues:789 är korrekt |
| STALE innebär inte att neighbor-posten redan är ogiltig | [iproute2-manual, Debian](https://manpages.debian.org/trixie/iproute2/ip-neighbour.8.en.html) | Repots förklaring i Issues:829 är korrekt; ingen nätverksorsak bevisas |
| Zero-serien är undantagen från den beskrivna lågspänningsdetekteringen | [Raspberry Pi power-supply warnings](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#power-supply-warnings) | Repots reservation om `throttled=0x0` är korrekt; readout bevisar inte frisk matning |

BTT:s HTML-tabell/wiki användes; ingen full schemagranskning eller lyckad PDF-rendering påstås. Namnplåtsdata för MOSO-PSU, specifik installerad hotend, Anycubic-ledningarnas spänningar, coilpolaritet och den exakta nivåomvandlaren är inte oberoende uppmätta eller externt verifierade här. De dokumenterade maskintesterna väger tyngre än ett annat projekts avvikande pinout.

PSU-uppgiften `MOSO MS-TB100J240-500C0`, 24 V / 21 A / 504 W, är internt aritmetiskt rimlig (`24 × 21 = 504`). Originalfotot finns inte i repot. Upgraderingskandidaternas ASIN/specifikationer är tydligt märkta som annonserade och ej installerade; de saknade produkt-PDF:erna har inte ersatts med nya annonser under auditen.

## Unresolved questions

1. **Slutlig live-cfg och mjukvaruversioner:** Vilken faktisk Klipper/KAMP/Mainsail/Moonraker-version och komplett konfiguration kördes senast? Den publicerade snapshoten räcker inte för att fastställa slutliga includes, ADXL, shaper, cleaner, pause/cancel, runout eller sparad kalibrering.
2. **PID-förvaltning:** Ska värdena förvaltas manuellt i inkluderade filer eller genom en genomtänkt SAVE_CONFIG-struktur? Det är ett framtida konfigurationsbeslut, inte skäl att ändra funktion under denna audit.
3. **Slutlig hotend och termistor:** Exakt modell, sensor/pull-up, temperaturgränser och slutlig PID/flow/PA saknas. Funktionellt bättre EPCOS-extrusion identifierar inte komponenten fysiskt.
4. **Elsystemets slutläge:** VUSB, Pi/hubbmatning, kortets faktiska jumper-/switchlägen och nivåomvandlarens modell kräver befintlig dokumentation eller fysisk inspektion. Ingen felorsak tillskrivs dessa utan belägg.
5. **LeviQ:** Vilket senare homing-/flerpunktsmesh-/startresultat avslutar den återöppnade tillförlitlighetsfrågan? Repot innehåller inget sådant avslut. Auditen föreskriver inte nya skrivartester.
6. **KAMP och geometri:** Vilken installerad purgeversion och vilka verkliga slicergränser gäller? A09 visar att en giltig printyta inte ensam garanterar giltiga extra purgeförflyttningar.
7. **Accelerometer och cleaner:** Slutlig Y-montageplats, sparad shaperkonfiguration, kontrollerad utskriftsjämförelse och faktisk cleaner-makro/kontakthöjd saknas.
8. **Historiskt underlag:** Exakta accelerationer, USB-förloppsdetaljer, PSU-fotot och källornas användningsdatum kan bara stärkas med material som faktiskt finns. Thingiverse 6247028:s specifika modellidentitet kunde inte bekräftas av läsbart innehåll i denna kontroll.

## Recommended fixes

Följande förslag bevaras från fas 1. Användarens senare beslut begränsar vilka delar som genomförs; utfallet står i fas 2-uppföljningen. Inget förslag ger tillstånd att ersätta maskinspecifika cfg-värden med generiska eller historiska alternativ.

### 1. Safety/technical correctness

- **A01:** Komplettera kalibreringsinstruktionen för split-config och `SAVE_CONFIG`. I första hand dokumentationsrättning; eventuell funktionell omstrukturering måste bedömas separat.
- **A03:** Förtydliga att 280/100 °C också är tillåtna programvarubörvärden. Skilj dessa från faktiska verifierade komponentgränser; välj inga nya siffror från antaganden.
- **A09/A11:** Behåll konkreta verifieringsfrågor om KAMP-kanten och fysisk board/power-setup. Ändra inte axelmax, matning eller jumpers automatiskt.

### 2. Contradictions

- **A02:** Ändra README:s snapshotetikett och länka till de redan bra dokumenterade förutsättningarna.
- **A04:** Byt tidsmarkören ”current” i PA-beskrivningen till att värdena kommer från den äldre snapshotens kalibrerade uppsättning.

### 3. Status drift

- **A13:** Skriv rapporterade/kommenderade accelerationer där faktisk uppnådd acceleration inte är belagd.
- Behåll reopened-status för LeviQ, Z-homing och nätverk. Behåll Input Shaper som utförd analys och cleaner som initial funktion. Om Hardware:s ”post-calibration print validation” förtydligas ska det avse den saknade kontrollerade jämförelsen, eftersom senare utskriftsobservationer redan finns.
- Gör inte historian falsk genom att byta tidig PA, reset-timing, TMC-värden eller gamla misslyckade kommandon till nya värden.

### 4. Duplication

- **A05:** Samla exakt USB-diagnostik i Issues och kabeltopologin i Wiring. Låt övriga dokument ha korta rollanpassade sammanfattningar/länkar.
- De små, överensstämmande UART-tabellerna behöver inte tas bort bara för att de förekommer på flera ställen. Ingen massrensning rekommenderas.

### 5. Formatting/cleanup

- **A06:** Reparera det interna pause/cancel-ankaret.
- **A07:** Precisera hotend-fläktens start-/avkylningsvillkor i Wiring och cfg-kommentaren.
- **A08:** Rätta tydliga små språkfel; justera rubriknivåer endast där navigeringen förbättras och behåll länkmålen.
- **A12:** Skriv inte om källhistoriken på antagande. Separera eventuella framtida auditkällor från källor som författaren faktiskt använde under bygget.

**Filer som sannolikt behöver små dokumentationsändringar:** README.md, docs/config/config.md, docs/Hardware.md, docs/Issues.md, docs/Wiring.md och docs/Journey.md. `docs/config/fans.cfg` berörs endast av en kommentar om A07 godkänns. Useful_links.md behöver endast ändras om författaren vill precisera källproveniens. Ingen funktionell cfg-ändring eller ändring av STL/bild är genomförd eller beslutad.

**Historisk stoppunkt efter fas 1:** Endast auditrapporten hade tillkommit; befintliga repositoryfiler var oförändrade. Fas 2 nedan genomfördes därefter enligt uttryckligt godkännande.


## Phase 2 status and validation

Fas 2 utgår från samma baseline `0a5059781347635a45b5196a254ad758e8c844a7` och denna fas 1-rapport. Branch: `docs/audit-phase-2-2026-09-17`. Ingen ny generell audit har gjorts.

**Status: 8 FIXED, 1 PARTIALLY FIXED, 4 ACCEPTED AS-IS / INFO, 0 DEFERRED.** Den ursprungliga severity-sammanställningen ovan beskriver fynden före rättning och är inte en lista över kvarvarande åtgärdskrav.

| Audit ID | Status | Ändrade filer utöver rapporten | Resolution |
|---|---|---|---|
| A01 | FIXED | [docs/config/config.md](./docs/config/config.md) | PID/control i inkluderade filer kan inte skrivas över automatiskt med SAVE_CONFIG. Guiden beskriver manuell lagring eller en kontrollerad övergång till genererade värden; heaterstruktur och PID-tal är oförändrade. |
| A02 | FIXED | [README.md](./README.md) | Snapshoten benämns machine-specific reference configuration snapshot med direkt länk till befintliga prerequisites/begränsningar. Historiska fungerande maskintester bevaras. |
| A03 | FIXED | [docs/config/config.md](./docs/config/config.md) | Förtydligad skillnad mellan avsedd drift, programvarans max_temp/tillåtna börvärden och verifierad komponentgräns. 280/100 och alla övriga cfg-tal bevaras. |
| A04 | FIXED | [docs/config/config.md](./docs/config/config.md), [docs/Hardware.md](./docs/Hardware.md), [docs/Journey.md](./docs/Journey.md) | Extrusionskalibreringen beskrivs som den äldre snapshotens setup. PA 0.44 och senare ~0.8 finns kvar som historik; ingen ny slutlig PA påstås. |
| A05 | FIXED | [docs/Hardware.md](./docs/Hardware.md), [docs/Wiring.md](./docs/Wiring.md), [docs/Journey.md](./docs/Journey.md), [docs/config/config.md](./docs/config/config.md) | Endast tydligt duplicerade USB-ID:n, felsträngar och kabeldetaljer har kortats/länkats. Wiring behåller kabeltopologi, Hardware identitet/montage, Issues detaljerna, Journey kronologin och config.md snapshot/live-skillnaden. |
| A06 | FIXED | [docs/Issues.md](./docs/Issues.md) | Ett stabilt explicit ankare före pause/cancel-sektionen matchar nu indexlänken. Befintliga rubriker bevaras. |
| A07 | FIXED | [docs/Wiring.md](./docs/Wiring.md), [docs/config/fans.cfg](./docs/config/fans.cfg) | Beskrivning och svensk kommentar anger aktivt target eller temperatur över heater_temp, även under avkylning. Ingen funktionell fan-rad ändrad. |
| A08 | PARTIALLY FIXED | [docs/Wiring.md](./docs/Wiring.md), [docs/Journey.md](./docs/Journey.md) | Uppenbara språkfel rättade. Generell H1/H2-omstrukturering har avsiktligt inte genomförts; samtliga befintliga rubriker/ankare är bevarade. |
| A09 | ACCEPTED AS-IS / INFO | — | Ingen separat ändring. KAMP-inställningar, purge, travel/printyta och slicergränser är oförändrade; villkorlig INFO kvar i denna rapport. |
| A10 | ACCEPTED AS-IS / INFO | — | Ingen separat ändring. A02 gör befintliga begränsningar synligare; inget serial-id, z_offset eller include har återställts. |
| A11 | ACCEPTED AS-IS / INFO | — | Ingen separat ändring och inga antaganden om VUSB, jumpers, matningsarkitektur eller nivåomvandlarmodell. |
| A12 | ACCEPTED AS-IS / INFO | — | Useful_links och byggens källhistorik är oförändrade. Auditkällan i kalibreringsguiden identifierar Klippers beteende och framställs inte som historisk byggkälla. |
| A13 | FIXED | [docs/Journey.md](./docs/Journey.md), [docs/config/config.md](./docs/config/config.md) | Höga accelerationstal beskrivs som requested, inte uppmätt uppnådda. Observationer om step loss bevaras för respektive test; slutsatsen om bevisad mekanisk marginal är borttagen. |

### Validering efter ändringarna

- Alla interna relativa filer/bilder/ankare kontrollerade, inklusive explicit pause/cancel-ankare. Inga brutna interna referenser hittades.
- Markdown-tabeller, code fences och details/summary kontrollerade utan fel. Alla befintliga rubriker i de sju ursprungliga Markdown-filerna är oförändrade.
- Samma statiska Klipper-nivå som fas 1: nio cfg-filer parsas enskilt och fem Jinja-mallar kompileras. Ingen full Klippy-/MCU-start har körts.
- Baseline och ändrade filer jämförda både radvis och som parsade cfg-optioner. **Noll funktionella cfg-rader ändrade.** Endast heater_fan-kommentaren i fans.cfg skiljer sig; alla pins, strömmar, koordinater, probe-värden, PID-tal, temperaturgränser och includes är oförändrade.
- Den tidigare fulla include-begränsningen (saknad mainsail.cfg), det saknade effektiva probe-offsetet, KAMP-beroendena och PID-save-konflikten reproduceras fortfarande. A01 rättar instruktionen och ändrar inte heaterstrukturen. Dessa förväntade begränsningar är inte nya regressionsfel.
- Varje ändrad källfil diffgranskad mot den angivna baseline-committen. Ingen massformatering, ingen ändring av Useful_links, bild eller STL och inga återställda äldre cfg-data.
- `git diff --check` utan anmärkning. Reopening-status för LeviQ och host/network samt äldre värden och misslyckanden är bevarade.

Fas 2 levereras som separat pull request från ovanstående branch. Vid skapad PR avslutas arbetet; ytterligare funktionell printerkonfiguration kräver separat godkännande. Ingen skrivare har kontaktats och ingen G-code har skickats.
