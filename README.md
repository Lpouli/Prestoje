# Prestoje

Webová aplikácia na zaznamenávanie prestojov výrobnej linky priamo z mobilu. Beží ako PWA — dá sa pridať na plochu telefónu, funguje aj bez signálu a dáta vie zdieľať s celým tímom cez Google Sheets.

Bez inštalácie, bez servera, bez knižníc — celá aplikácia je jeden HTML súbor.

---

## Čo vie

- **Vlastné polia** — stĺpce si nadefinuješ sám (text, číslo, dátum a čas, automaticky počítané trvanie). Formulár sa im prispôsobí.
- **Pracovné zmeny** — ľubovoľný počet, vrátane nočnej cez polnoc. Zoznam prestojov sa filtruje podľa prebiehajúcej zmeny.
- **Prebiehajúce prestoje** — zapíšeš prestoj bez konca, ostane oranžovo hore s bežiacim časom a jedným ťuknutím ho ukončíš.
- **Obojsmerný výpočet** — zadáš koniec, dopočíta sa trvanie; zadáš trvanie, dopočíta sa koniec.
- **Zvýraznenie dlhých prestojov** — nastavíš hranicu v minútach a všetko nad ňu svieti červeno v appke aj v exporte.
- **Export do Excelu** — skutočný `.xlsx` s nastaviteľným poradím stĺpcov. Dátumy a trvania sú reálne hodnoty, takže sa dajú sčítať a filtrovať.
- **Zdieľanie cez Google Sheets** — prestoje aj nastavenia sa synchronizujú medzi zariadeniami. Zapisovať sa dá aj offline.
- **Offline režim** — zápis funguje bez pripojenia, zmeny sa odošlú, keď je signál späť.

## Obsah repozitára

| Súbor | Popis |
|---|---|
| `index.html` | Celá aplikácia — UI, logika, synchronizácia aj generovanie `.xlsx` |
| `sw.js` | Service worker pre offline režim |
| `manifest.webmanifest` | PWA manifest |
| `icon-192.png`, `icon-512.png` | Ikony aplikácie |
| `google-apps-script.gs` | Serverová časť pre zdieľanie cez Google Sheets (nasadzuje sa v Apps Script, nie na web) |

## Rýchle spustenie

Otvor `index.html` v prehliadači. Funguje všetko vrátane exportu, dáta sa ukladajú lokálne.

## Nasadenie na GitHub Pages

1. Nahraj do repozitára `index.html`, `sw.js`, `manifest.webmanifest` a obe ikony.
2. **Settings → Pages** → *Source:* **Deploy from a branch**, vetva `main`, priečinok `/ (root)` → **Save**.
3. Po chvíli je aplikácia na `https://pouzivatel.github.io/nazov-repozitara/`.
4. Na telefóne otvor adresu v Chrome → ⋮ → **Pridať na plochu**.

Repozitár musí byť verejný — GitHub Pages pre súkromné repozitáre je platená funkcia. Adresa Google Sheets sa v kóde nenachádza, zadáva sa v nastaveniach aplikácie, takže zverejnený kód nič neprezrádza.

## Zdieľané dáta cez Google Sheets

Bez tohto kroku má každé zariadenie vlastné dáta. Nastavuje sa raz, cca 5 minút.

1. Vytvor nový Google Sheet, napr. „Prestoje – dáta“. V **Súbor → Nastavenia** nastav časové pásmo.
2. V tabuľke otvor **Rozšírenia → Apps Script**.
3. Vymaž obsah editora, vlož doň celý `google-apps-script.gs` a ulož. V **Nastaveniach projektu** skontroluj rovnaké časové pásmo.
4. **Nasadiť → Nová implementácia** → typ **Webová aplikácia** → *Spustiť ako:* **Ja** → *Kto má prístup:* **Ktokoľvek** → Nasadiť.
5. Potvrď prístup k účtu (pri hlásení „Aplikácia nie je overená“ klikni *Rozšírené → Prejsť na…*) a skopíruj adresu končiacu na `/exec`.
6. V aplikácii choď do **Nastavenia → Zdieľanie**, vlož adresu a zapni zdieľanie. Rovnakú adresu zadaj na ostatných zariadeniach.

**Ako synchronizácia funguje**

- Zapisuje sa vždy najprv lokálne, preto appka funguje aj bez signálu.
- Synchronizuje sa po každom zápise, pri otvorení aplikácie, pri návrate signálu a inak raz za minútu. Stav vidno v hlavičke.
- Pri konflikte vyhráva novšia zmena (podľa časovej pečiatky). Vymazanie sa prenáša tiež — riadok ostáva v tabuľke s príznakom *Vymazané*.
- Zdieľajú sa aj nastavenia polí, zmien a exportu, takže stačí nastaviť ich na jednom zariadení.
- V tabuľke pribudne hárok `Prestoje` (dáta) a skrytý `_meta` (nastavenia). Do dát sa dá bežne filtrovať a robiť kontingenčné tabuľky — len needituj stĺpce `id` a `Zmenené`, podľa nich sa synchronizácia rozhoduje.

> **Bezpečnosť:** kto pozná adresu `/exec`, môže dáta čítať aj zapisovať. Drž ju v rámci tímu a nedávaj do repozitára.

## Dáta a zálohy

Záznamy sú uložené v prehliadači zariadenia (localStorage). Vymazanie dát prehliadača ich zmaže, preto v **Nastavenia → Dáta** občas použi **Záloha** (uloží JSON) a **Obnoviť zo zálohy**. Pri zapnutom zdieľaní sú dáta navyše aj v Google tabuľke.

## Technické poznámky

- Žiadne závislosti a žiadny build. `.xlsx` sa skladá priamo v prehliadači vlastným ZIP a OOXML zapisovačom, takže export funguje aj offline.
- Aplikácia sa načítava zo siete a z pamäte len ako záloha, takže nová verzia sa prejaví hneď po nahratí.
- Testované v Chrome na Androide a na desktope.

## Plán do budúcna

- Presun úložiska na Supabase (prihlásenie, práva, rýchlosť pri veľkom počte záznamov)
- Prehľady priamo v aplikácii — najčastejšie príčiny, súčty po linkách, porovnanie zmien
- Natívna Android aplikácia
