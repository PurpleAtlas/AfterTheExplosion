# AfterTheExplosion

Strategická hra v reálném čase (s pauzou) o světě po nukleární apokalypse roku 1968.
Postavená jako fork open source projektu **Pulsar4X** (C#, .NET 8, MIT licence).

Autor a vlastník projektu: Karel (GitHub `PurpleAtlas`). Komunikuje česky.

---

## Pravidla spolupráce

- S Karlem komunikuj **česky**. Kód, názvy tříd a commit zprávy piš anglicky.
- Piš krátce a věcně. Žádné vychvalování, žádné korporátní fráze.
- Nikdy nepoužívej em dash (dlouhou pomlčku). Jen běžnou pomlčku nebo tečku.
- U větších změn **nejdřív navrhni osnovu, počkej na schválení**, teprve pak implementuj.
- Když není zadání jasné, nejdřív se zeptej.
- Když něco doporučuješ, vždy vysvětli proč.
- Po každém dokončeném kroku: ověř build (`dotnet build`), pokud to jde, hru spusť, a commitni s jasnou zprávou.
- Soubor `LICENSE` (MIT, copyright Pulsar4xDevs) musí zůstat zachovaný.

---

## Vize hry

- **Rok 1968.** Alternativní historie: v roce 1968 proběhla globální nukleární válka. Státy se rozpadly.
- Svět se znovu formuje. Na troskách vznikají **kmeny, kulty a bratrstva**, ze kterých postupně vyrůstají nové národy.
- Technika, estetika a jazyk odpovídají konci 60. let. Žádná moderní technologie. Výzkum = **znovuobjevování** předválečné techniky (najdeš vysílačku v ruinách, ale musíš ji umět opravit a později vyrobit).

### Klíčová rozhodnutí (schválená)

| Oblast | Rozhodnutí |
|---|---|
| Technologie | Zůstáváme u **C# / .NET 8**, desktop (ImGui + SDL2 + OpenGL z Pulsaru) |
| Planeta | **Vždy reálná Země**, ale při každé nové hře **lehce jinak** (posunutá pobřeží, krátery, zatopené oblasti, radiační zóny, jiné ruiny) |
| Frakce | Při každé hře **kompletně jiné rozložení frakcí** (procedurálně) |
| Mapa | **Volný terén** (žádné hexy ani provincie) s **dynamicky se měnícími hranicemi** |
| Čas | **Real-time s pauzou** a zrychlením (jako původní Pulsar) |

---

## Principy převzaté z dynamické kampaně Falcon 4.0

Tyto principy jsou páteří herního designu. Priorita: **1, 3, 4 jako základ, hned potom 2.**

1. **Svět jede sám.** Frakce válčí, obchodují a rostou bez ohledu na hráče. Hráč je součást světa, ne jeho střed.
2. **Síť objektů se závislostmi.** Čerpadlo drží osadu naživu, rafinerie dává palivo karavanám, elektrárna napájí dílny. Zničení jednoho uzlu se propaguje dál.
3. **Fyzické zásobování.** Karavany jezdí po cestách a dají se přepadnout. Frakce bez paliva, vody nebo jídla slábne. Hranice se posouvají i díky logistice, nejen bitvám. Stavět na modulu `Logistics`.
4. **Simulační bublina (agregace / deagregace).** Kolem hráče detailní simulace (jednotlivé skupiny, karavany), daleko agregovaná (frakce = pár čísel). Nutné kvůli výkonu celé planety v reálném čase.
5. **Hráč nastavuje priority, ne každý krok.** Vůdce frakce nastaví priority (jídlo, obrana severu, průzkum) a AI rozdělí úkoly. Do detailu hráč zasahuje, jen když chce.
6. **Nástěnka úkolů** (obdoba ATO z Falconu). AI generuje úkoly: doprovod karavany, průzkum ruin, vyjednávání s kultem. Hráč si vybírá.
7. **Trvalé škody a pomalé opravy.** Spálená osada zůstane ruinou, dokud ji někdo neobnoví. Svět má paměť.
8. **Zprávy ve stylu doby.** Rádiová hlášení, letáky, kroniky kultů. Každá frakce podává stejnou událost po svém.
9. **Mlha války.** Informace mimo vlastní území stárnou. Průzkum a rádio je obnovují. Stavět na `Sensors` a `GeoSurveys`.
10. **Veteráni a vůdci.** Pojmenované postavy se zkušenostmi a historií. Ztráta mechanika = ztráta know-how. Stavět na `People`.

---

## Osnova vývoje (fáze)

**Fáze 0: Čistý start**
- Rozběhnout build původního Pulsaru (`Pulsar4X/Pulsar4X.sln`).
- Odstranit vesmírné části (viz níže). Cíl: hra se spustí s prázdnou mapou a běžícím herním časem.

**Fáze 1: Planeta**
- Generátor reálné Země s náhodnými odchylkami: výšky, pobřeží, řeky, klima, biomy.
- Post-apo vrstva: krátery, radiační zóny, ruiny měst, zničená infrastruktura (silnice, železnice, mosty).
- Klient: 2D mapa planety s posunem a zoomem místo mapy hvězdného systému.

**Fáze 2: Území, hranice a simulační bublina**
- Osady vyzařují vliv (populace, síla, terén; hory a řeky ho brzdí).
- Jemná vlivová mřížka pod mapou, hranice jako plynulé obrysy, přepočet v herním čase.
- Agregace / deagregace (princip 4) navrhnout už tady.
- Milník: **první prototyp** (planeta + osady + živé hranice + svět jede sám).

**Fáze 3: Ekonomika přežití a síť závislostí**
- Suroviny: jídlo, voda, palivo, šrot, munice, léky, předválečná technika.
- Populace, růst, hlad, nemoci.
- Graf závislostí mezi objekty (princip 2).
- Fyzické zásobování karavanami (princip 3).
- Co nejvíc řešit úpravou JSON dat v `GameEngine/Data/basemod`.

**Fáze 4: Frakce**
- Typy: kmen, kult, bratrstvo (každý jinak roste, bojuje a jedná se sousedy).
- Procedurální rozložení frakcí při každé hře.
- Základ AI a systém priorit (princip 5).

**Fáze 5: Pohyb a jednotky**
- Pozemní pohyb po terénu a cestách, pathfinding.
- Karavany, ozbrojené skupiny, přepady, střety.
- Nástěnka úkolů (princip 6).

**Fáze 6: Technika z roku 1968**
- Výzkumný strom jako znovuobjevování (modul `Tech`).

**Fáze 7: Diplomacie a události**
- Spojenectví, tributy, náboženské konverze, radiační bouře, epidemie.
- Zprávy ve stylu doby (princip 8), mlha války (princip 9), veteráni (princip 10).

**Fáze 8: UI a vizuál**
- Styl konce 60. let: zelené monitory, psací stroj, vojenské mapy, rádio.

---

## Mapa původního kódu Pulsar4X

Řešení: `Pulsar4X/Pulsar4X.sln`. Hlavní projekty:

- `Pulsar4X/GameEngine` - herní logika (ECS). **Základ, který zachováváme.**
- `Pulsar4X/Pulsar4X.Client` - klient (ImGui + SDL2 + OpenGL). Vykreslování galaxie a systému půjde většinou pryč.
- `Pulsar4X/Pulsar4X.OrbitalMath` - orbitální mechanika. **Ke zrušení** (nejdřív odpojit závislosti).
- `Pulsar4X/Pulsar4X.Tests`, `Benchmarks`, `DebugReloadClient`, `ViewModelLib` - podpůrné.

### Co zachovat a přepsat

| Modul Pulsaru | Nová role |
|---|---|
| `Engine` (ECS, datablobs, processory, save/load, modding) | Jádro beze změny |
| `Colonies` | Osady |
| `Factions` | Kmeny, kulty, bratrstva |
| `Industry`, `Storage` | Ekonomika přežití |
| `Logistics` | Karavany a zásobování |
| `Tech` | Znovuobjevování techniky |
| `People` | Vůdci, veteráni, specialisté |
| `GeoSurveys` | Průzkum ruin a radiačních zón |
| `Sensors` | Mlha války, průzkum |
| `Weapons`, `Damage` | Zbraně a boj (zjednodušit na pozemní) |
| `Fleets` | Skupiny a karavany |
| `Data/basemod` | Nová data (suroviny, blueprinty, technologie) |

### Co odstranit

- `Orbits`, `JumpPoints`, `Galaxy`
- `Movement/NewtonMove`, `Movement/WarpMove` (nahradit pozemním pohybem; `Pathfinding` zvážit k přepoužití)
- `Pulsar4X.OrbitalMath`
- `Ships` (část přepoužít jako vozidla / jednotky, zbytek pryč)
- `Energy` (posoudit, možná jako elektřina pro osady)
- Klient: `GalacticMapRender.cs`, `GalaxyMap.cs`, `SystemMapRendering.cs`, `ManuverNodes` a související UI

Odstraňuj postupně a po každém kroku ověř, že build prochází.

---

## Build a spuštění

- Potřeba: .NET 8 SDK (viz `Pulsar4X/global.json`), Git.
- Build: `dotnet build Pulsar4X/Pulsar4X.sln`
- Spuštění klienta: `dotnet run --project Pulsar4X/Pulsar4X.Client`
- Závislosti z NuGetu: ImGui.NET, Newtonsoft.Json, CoreCLR-NCalc, NUnit a další (viz `*.csproj`).
