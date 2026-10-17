# Master Golf - Väylä- ja Korkeusmittari (Projektimuistio)

Tämä tiedosto toimii projektin pääohjeistuksena ja muistiona kehittäjälle ja tekoälyavustajalle (Gemini/VS Code). Lue tämä tiedosto aina uuden istunnon alussa ymmärtääksesi sovelluksen tarkoituksen, toimintaperiaatteet ja säännöt.

> ⚠️ **TÄRKEÄ SÄÄNTÖ UUDEN CHATIN ALOITUKSEEN:**  
> Aina kun käyttäjä aloittaa uuden keskustelun/chatin, tekoälyn tulee **välittömästi lukea ja huomioida tiedostot `PROJECT.md` ja `index.html`**. Työskentelyä ja kehitystä jatketaan aina suoraan näiden tiedostojen ja muistiossa olevan ToDo-listan pohjalta ilman, että käyttäjältä kysytään mitä olimme tekemässä. 
>Kun teet muutosehdotuksen koodiin, niin jotta muutos tehdään oikeaan kohtaan, niin näytä selkeästi ennen ja jälkeen 2 riviä säilytettävää koodia

---

## 1. Sovelluksen Kuvaus ja Tarkoitus
- **Nimi:** Master Golf - Väylä- ja Korkeusmittari (v6.64.0)
- **Tarkoitus:** Mobiilioptimoitu (PWA) golfin etäisyys-, sektori-, sää-, korkeus- ja lyöntianalyysimittari (Strokes Gained / SG).
- **Käyttökokemus:** Suunniteltu käytettäväksi älypuhelimella suoraan kentällä (helppokäyttöiset painikkeet, selkeät kontrastit, kosketusnäyttöystävällinen).

---

## 2. Tekninen Ympäristö ja Riippuvuudet
- **Rakenne:** Kevyt ja nopea yhden tiedoston arkkitehtuuri (`index.html`), joka sisältää HTML:n, CSS:n ja Vanilla JavaScriptin.
- **Karttakirjasto:** [MapLibre GL JS](https://maplibre.org/) (v3.6.2) ja HTML5 Canvas -päällyskerros sektoreille.
- **Karttatiili (karttapohja):** Google Satellite Hybrid (`https://mt{s}.google.com/vt/lyrs=s,h...`).
- **Ulkopuoliset API:t:**
  - **Open-Meteo Weather API:** Reaaliaikainen tuulen nopeus, puuskat, tuulensuunta ja lämpötila tiipaikan koordinaateista.
  - **Open-Meteo Elevation API:** Tiipaikan ja kohteen/viheriön korkeuseron laskeminen merenpinnasta (DEM-data).
  - **Google Maps Elevation API:** Varajärjestelmä korkeusdatalle (avaimella).
  - **Nominatim & Overpass API:** Golfkenttien ja väylien haku.
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
4. **Mobiiliyhteensopivuus:** Varmista, että kosketuskomennot (`touchmove`, `touchstart`) ja painikkeiden klikkaukset eivät aiheuta kartan tahatonta siirtymistä (pan/zoom).
5. **Kieli:** Käyttöliittymä ja käyttäjäviestintä ovat suomeksi.
6. **Projektimuistio:** Pidä `PROJECT.md` ajan tasalla aina kun merkittäviä ominaisuuksia lisätään tai logiikkaa muutetaan.
7. **Git-ohjeet:** Muutosten jälkeen käyttäjälle kerrotaan selkeästi mitä muutettiin ja annetaan nopeat git-komennot viemiseen GitHubiin.

---

## 5. ToDo-lista ja Tulevat Kehitysaskeleet

### 5.1 Mitä teemme seuraavaksi (Prioriteetti 1)
- [ ] **Mobiilitestaus:** Varmistetaan kenttäkäytössä / puhelimella, että v6.59.0:ssa tehdyt CSS safe-area (`max()`) ja kartanäkymän tiivistys (`fitBounds`) istuvat täydellisesti ilman hukkaneliöitä ylä- ja alareunassa.
- [ ] **Asetukset / Säädöt:** Tehdään kevyt asetusnäkymä tai -painike, josta käyttäjä voi säätää:
  - Par 3 -rajaetäisyyttä (nykyinen kiinteä `200m`).
  - Pelaajan omaa draivin oletuspituutta ja hajontaa.

### 5.2 Jatko-ominaisuudet (Backlog)
- [ ] **Väylänavigaattori:** Kentän väylien nopea valinta (1–18) ilman manuaalista hakua tai kartan raahausta.
- [ ] **Lyöntihistoria ja Strokes Gained -seuranta:** Mahdollisuus merkitä lyöntejä kierroksen aikana ja tallentaa tilastot paikallisesti (`localStorage`).
- [ ] **Offline-tuki / PWA Cache:** Service worker, joka tallentaa sovelluksen rungon offline-käyttöä varten.
