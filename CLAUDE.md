# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Projekti

Sudoku-peli, jonka koko pelikoodi on `index.html`-tiedostossa (HTML + CSS + JavaScript). PWA-tuki erillisillä tiedostoilla (`manifest.json`, `sw.js`, `icons/`). Ei rakennustyökaluja, ei riippuvuuksia, ei palvelinta — avataan suoraan selaimessa tai GitHub Pagesilta.

## Arkkitehtuuri

Pelikoodi on `index.html`-tiedostossa kolmessa osassa:

1. **CSS** (`<style>`-osio) — CSS-muuttujat väriteemalle, responsiivinen asettelu mobiilille
2. **Peli-JS** (ensimmäinen `<script>`-lohko, IIFE) — koko pelilogiikka:
   - `generatePuzzle()` → `fill()` (rekursiivinen backtracking) + `countSolutions()` (ratkaisun yksikäsitteisyyden varmistus)
   - `updateVisuals()` — renderöi koko laudan tilan joka muutoksen jälkeen (ei valikoivaa päivitystä)
   - `findConflicts()` — palauttaa Set<index> konfliktiruuduille
   - `setNum()` — käsittelee sekä normaalin syötön että muistiinpanotilan (pencil mode)
   - Undo-pino tallentaa koko pelaajatilan + muistiinpanot per siirto
   - Ajastin perustuu `Date.now()`-aikaleimaan (ei intervallitikityksiin), jotta aika pysyy oikeana vaikka Chrome throttlaa taustalla olevan välilehden intervallit
3. **SW-rekisteröinti** (toinen `<script>`-lohko) — rekisteröi service workerin (`sw.js`)

PWA-tiedostot:

- `manifest.json` — `start_url: "."`, `display: "standalone"`
- `icons/` — SVG-ikonit: `icon-192.svg`, `icon-512.svg` ja `icon-maskable.svg`
- `sw.js` — network first -strategia: haetaan aina ensin verkosta, välimuisti vain offline-fallbackina. Vain GET- ja same-origin-pyynnöt välimuistitetaan; navigaatiopyynnöt palautuvat offline-tilassa `index.html`-tiedostoon.

## Keskeiset tietorakenteet

- `puzzle[9][9]` — alkuperäinen palapeli (0 = tyhjä, annetut numerot pysyvät)
- `player[9][9]` — pelaajan tila (kopio puzzlesta + pelaajan syötteet)
- `solution[9][9]` — oikea ratkaisu (ei käytetä voiton tarkistukseen — voitto todetaan konfliktitarkistuksella kun kaikki ruudut täynnä)
- `pencilMarks[9][9]` — Set-olioita per ruutu muistiinpanoille
- `cellElements[]` — DOM-elementit järjestyksessä (r*9+c -indeksillä)

## Vaikeustasojen vihjemäärät

- Helppo: 40 vihjettä
- Keskitaso: 32 vihjettä
- Vaikea: 24 vihjettä

## Kehitys

Ei rakennusvaihetta. Avaa `index.html` suoraan selaimessa. Muutokset näkyvät heti sivua päivittämällä.
