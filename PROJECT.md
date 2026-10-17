# Master Golf - Väylä- ja Korkeusmittari (Projektimuistio)

Tämä tiedosto toimii projektin pääohjeistuksena ja muistiona kehittäjälle ja tekoälyavustajalle (Gemini/VS Code). Lue tämä tiedosto aina uuden istunnon alussa ymmärtääksesi sovelluksen tarkoituksen, toimintaperiaatteet ja säännöt.

---

## 1. Sovelluksen Kuvaus ja Tarkoitus
- **Nimi:** Master Golf - Väylä- ja Korkeusmittari
- **Tarkoitus:** Mobiilioptimoitu (PWA) golfin etäisyys-, sektori-, sää-, korkeus- ja lyöntianalyysimittari (Strokes Gained / SG).
- **Käyttökokemus:** Suunniteltu käytettäväksi älypuhelimella suoraan kentällä (helppokäyttöiset painikkeet, selkeät kontrastit, kosketusnäyttöystävällinen).

---

## 2. Tekninen Ympäristö ja Riippuvuudet
- **Rakenne:** Kevyt ja nopea yhden tiedoston arkkitehtuuri (`index.html`), joka sisältää HTML:n, CSS:n ja Vanilla JavaScriptin.
- **Karttakirjasto:** [Leaflet.js](https://leafletjs.com/) (v1.9.4).
- **Karttatiili (karttapohja):** Google Satellite Hybrid (`https://{s}.google.com/vt/lyrs=s,h...`).
- **Ulkopuoliset API:t:**
  - **Open-Meteo Weather API:** Reaaliaikainen tuulen nopeus, puuskat, tuulensuunta ja lämpötila tiipaikan koordinaateista.
  - **Open-Meteo Elevation API:** Tiipaikan ja kohteen/viheriön korkeuseron laskeminen merenpinnasta (DEM-data).
- **Versiohallinta ja julkaisu:** Git & GitHub (GitHub Pages mobiilikäyttöä varten).

---

## 3. Päätoiminnallisuudet ja Laskentasäännöt

### 3.1 Merkinnät ja Kohteet
- **Tii (Tee):** Lähtöpaikka (vihreä tiikuvake). Voidaan siirtää manuaalisesti tai asettaa puhelimen GPS-sijaintiin.
- **Viheriö (Green):** Reiän sijainti (punainen lippukuvake).
- **Kohde (Target):** Vapaasti liikuteltava tähtäyspiste väylällä.
  - Normaali tila: Siirtyy vapaasti kosketuksella/osoittimella.
  - Lukittu tila (🔒): Lukitsee etäisyyden tiiltä ja mahdollistaa sektorin kääntämisen säilyttäen etäisyyden.

### 3.2 Lyöntisektorit ja Kaaret
- **Sisäsektori (Inner):** Kapeampi hajontasektori (kerroin n. 0.1481 suhteessa etäisyyteen, katkoviiva).
- **Ulkosektori (Outer):** Leveämpi hajontasektori (kerroin n. 0.2222 suhteessa etäisyyteen, yhtenäinen viiva).
- **Etäisyyskaaret:** Piirretään 130 metristä alkaen 30 metrin välein sektorin sisälle.
- **Päänuoli ja Mittamerkki (Badge):**
  - Nuoli osoittaa lyöntisuunnan.
  - Infomerkki näyttää lyöntimatkan (m), korkeuseron (m) sekä jäljellä olevan matkan viheriölle (⛳ m).

### 3.3 "Plays Like" -laskentalogiikka
Sovellus laskee efektiivisen suosituslyöntipituuden kaavalla:
`Plays Like = Todellinen matka + Tuulivaikutus + Lämpötilavaikutus + Korkeusvaikutus`
- **Tuuli:** Laskee vasta- tai myötätuulikomponentin kulman mukaan suhteessa lyöntisuuntaan.
- **Lämpötila:** Vertailukohtana +20°C (kylmä ilma lyhentää palloa).
- **Korkeusero:** Ylämäki lisää vaadittavaa mittaa, alamäki vähentää.

### 3.4 Strokes Gained (SG) -laskenta
- **Periaate:** Strokes Gained arvioi lyönnin arvoa suhteessa vertailutasoon.
- **Kaava:** `SG = Odotusarvo(lähtöpaikka) - Odotusarvo(päätepaikka) - 1 lyönti`
- **Toiminnot:** Tiilyönnin ja lähestymisen vertailu etäisyyden ja alustan mukaan (Väylä vs. Raffi/Bunkkeri).

---

## 4. Kehityssäännöt ja Hyvät Käytännöt (TÄRKEÄT SÄÄNNÖT TEKOÄLYLLE)
1. **SUUNNITELMA ENNEN MUUTOKSIA:** Ennen kuin teet mitään muutoksia koodiin, esitä selkeä suunnitelma ja **hyväksytä se aina käyttäjällä**. Muutoksia koodiin saa tehdä vasta, kun käyttäjä on antanut hyväksynnän.
2. **Pidä tiedostorakenne selkeänä:** Sovellus pidetään toistaiseksi ensisijaisesti `index.html`-tiedostossa ellei erikseen sovita modularisoinnista.
3. **Versionumerointi:** Päivitä aina sovelluksen versiotunniste sekä HTML:n alapaneeliin että JS-muuttujaan `APP_VERSION`, kun teet toiminnallisia muutoksia.
4. **Mobiiliyhteensopivuus:** Varmista, että kosketuskomennot (`touchmove`, `touchstart`) ja painikkeiden klikkaukset eivät aiheuta Leaflet-kartan tahatonta siirtymistä (pan/zoom).
5. **Kieli:** Käyttöliittymä ja käyttäjäviestintä ovat suomeksi.
6. **Projektimuistio:** Pidä `PROJECT.md` ajan tasalla aina kun merkittäviä ominaisuuksia lisätään tai logiikkaa muutetaan.
7. **Git-ohjeet:** Muutosten jälkeen käyttäjälle kerrotaan selkeästi mitä muutettiin ja annetaan nopeat git-komennot viemiseen GitHubiin.
