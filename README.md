# Mila — Glamping A-frame na Zlatiboru

Statičan sajt, tri stranice. Nema build koraka ni zavisnosti. Fontovi (Marcellus, Karla) se učitavaju sa Google Fonts.

## Struktura

```
landing/index.html     /landing     početna (forma za upit)
thank-you/index.html   /thank-you   thank you stranica posle poslatog upita (noindex)
utisci/index.html      /utisci      utisci gostiju
index.html             /            redirect na /landing
images/                hero.webp, enterijer.webp, terasa.webp, terasa-2.webp, spavaci.webp, dusk.webp, mapa.webp
```

Svi linkovi i slike su relativni (`../images/`, `../thank-you/`), pa sajt radi i u podfolderu (GitHub Pages project site) i na root domenu.

## Hosting

Bilo koji statički hosting radi (Netlify, Vercel, Cloudflare Pages, GitHub Pages, običan cPanel). Ceo folder ide u root. Trenutno: GitHub Pages, grana `main`, root folder.

## Pre puštanja uživo

1. Forma za upit (`#upit` u landing/index.html) šalje sve podatke na Make webhook (`WEBHOOK_URL` u skripti na dnu), Make ih upisuje u Google Sheet. Polja iz forme: ime, email, telefon, gosti, dolazak, odlazak, poruka. Dodatno: gosti_tekst, dolazak_format, odlazak_format, broj_noci, vreme_slanja, vreme_slanja_iso, jezik, stranica, referrer, utm_source/medium/campaign/term/content, fbclid, gclid, uredjaj, user_agent. Make scenario mora biti uključen, inače webhook vraća 410 i forma prikazuje grešku.
2. Dugmad "Rezerviši na Booking", "Pogledaj na Airbnb" i "Proveri dostupnost" imaju `href="#"`. Ubaciti prave linkove.
3. Futer na obe stranice: zameniti `[telefon]`, `[email]`, `[@instagram]`.
4. Slike su WebP (quality 82, ukupno oko 1 MB), konvertovane iz originalnih PNG-ova (13 MB).

## Jezik

SR/EN prekidač radi preko `data-en` atributa na elementima. Izabrani jezik se pamti u localStorage (`mila_lang`).

## Meta Pixel

Pixel ID `1097774762580497` je u `<head>` sve tri stranice.

- `/landing`, `/utisci`: `PageView`.
- `/thank-you`: `PageView` + `Purchase` (value 49, currency USD).

Tok: forma šalje upit na Make webhook, i tek kad webhook vrati uspeh, posetilac ide na `/thank-you/?upit=<ID>`. Purchase se okida samo kad postoji parametar `upit`, sa `eventID = purchase-<ID>`. Isti ID ide u Make kao polje `event_id` (za buduću deduplikaciju sa Conversions API-jem). Posle okidanja parametar se briše iz adrese, pa refresh ne duplira Purchase. Direktno otvaranje `/thank-you` daje samo PageView.

