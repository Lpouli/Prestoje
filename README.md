# Prestoje

Webová aplikácia na zaznamenávanie prestojov výrobnej linky priamo z mobilu. Beží ako PWA — dá sa pridať na plochu telefónu, funguje aj bez signálu a dáta vie zdieľať s celým tímom cez Google Sheets.

Bez inštalácie, bez servera, bez knižníc — celá aplikácia je jeden HTML súbor.

---

## Čo vie

- **Vlastné polia** — stĺpce sa definujú v nastaveniach (text, číslo, dátum a čas, automaticky počítané trvanie) a formulár sa im prispôsobí.
- **Pracovné zmeny** — ľubovoľný počet, vrátane nočnej cez polnoc. Zoznam prestojov sa filtruje podľa prebiehajúcej zmeny.
- **Prebiehajúce prestoje** — prestoj bez vyplneného konca ostáva zvýraznený hore s bežiacim časom a ukončí sa jedným ťuknutím.
- **Obojsmerný výpočet** — z konca prestoja sa dopočíta trvanie, zo zadaného trvania zase koniec.
- **Zvýraznenie dlhých prestojov** — podľa nastavenej hranice v minútach, v aplikácii aj v exporte.
- **Export do Excelu** — skutočný `.xlsx` s nastaviteľným poradím stĺpcov. Dátumy a trvania sú reálne hodnoty, dajú sa sčítať a filtrovať.
- **Zdieľanie cez Google Sheets** — prestoje aj nastavenia sa synchronizujú medzi zariadeniami.
- **Offline režim** — zápis funguje bez pripojenia, zmeny sa odošlú po návrate signálu.

## Ako to funguje

Záznamy sa ukladajú do prehliadača zariadenia (localStorage), takže aplikácia je použiteľná okamžite a bez pripojenia.

Ak je zapnuté zdieľanie, obsah sa navyše synchronizuje s hárkom v Google Sheets cez Apps Script:

- synchronizácia prebieha po každom zápise, pri otvorení aplikácie, pri návrate signálu a inak raz za minútu,
- pri konflikte vyhráva novšia zmena podľa časovej pečiatky,
- vymazanie sa prenáša tiež — riadok ostáva v tabuľke s príznakom *Vymazané*,
- zdieľajú sa aj nastavenia polí, zmien a exportu, takže stačí nastaviť ich na jednom zariadení,
- v tabuľke vzniká hárok `Prestoje` s dátami a skrytý `_meta` s nastaveniami. Dáta sú pripravené na filtre a kontingenčné tabuľky; stĺpce `id` a `Zmenené` slúžia synchronizácii.

> **Bezpečnosť:** kto pozná adresu nasadeného Apps Scriptu, môže dáta čítať aj zapisovať. Adresa nie je súčasťou kódu, zadáva sa v nastaveniach aplikácie.

## Obsah repozitára

| Súbor | Popis |
|---|---|
| `index.html` | Celá aplikácia — UI, logika, synchronizácia aj generovanie `.xlsx` |
| `sw.js` | Service worker pre offline režim |
| `manifest.webmanifest` | PWA manifest |
| `icon-192.png`, `icon-512.png` | Ikony aplikácie |
| `google-apps-script.gs` | Serverová časť pre zdieľanie cez Google Sheets (nasadzuje sa v Apps Script, nie na web) |
| `NAVOD.md` | Nasadenie na GitHub Pages, zapnutie zdieľania a inštalácia na telefón |

## Technické poznámky

- Žiadne závislosti a žiadny build. `.xlsx` sa skladá priamo v prehliadači vlastným ZIP a OOXML zapisovačom, takže export funguje aj offline.
- Aplikácia sa načítava zo siete a uložená kópia slúži ako záloha, preto sa nová verzia prejaví hneď po nahratí.
- Testované v Chrome na Androide a na desktope.

## Plán do budúcna

- Presun úložiska na Supabase (prihlásenie, práva, rýchlosť pri veľkom počte záznamov)
- Prehľady priamo v aplikácii — najčastejšie príčiny, súčty po linkách, porovnanie zmien
- Natívna Android aplikácia
