# Tæt på

Dette repo er GitHub Pages-siden https://socialcomplexitylab.github.io/taet-paa/ . `index.html` er hele sitet.
Når Sune beder dig bygge siden, er opgaven denne:

Byg en hjemmeside, der er reelt nyttig for københavnere: "Tæt på". Man skriver en adresse i
København (eller trykker "Hvor er jeg?", eller klikker på kortet), og siden viser alt det,
kommunen ved om netop det sted, samlet ét sted:

1. Træerne udenfor: de fem nærmeste kommunale træer med dansk navn, art og planteår, og
   det ældste træ inden for 300 m.
2. Hvad der sker på gaden lige nu og de næste uger: gravearbejder, arrangementer,
   filmoptagelser, containere, udeservering, vejspærringer, med datoer, og hvem der graver.
3. Trafikmeldinger tæt på.
4. Trafikstøj ved facaden (Lden og Lnight i dB; WHO's vejledende grænse for vejstøj er 53 dB
   Lden).
5. Tagets solcellepotentiale (MWh/år og kommunens vurdering).
6. Skybrud: står stedet under vand ved en 100-års-regn, i dag og i 2110? Og ved en
   100-års-stormflod?
7. Overfladetemperatur en sommerdag (gennemsnit seneste tre år), som et mål for varmeø.
8. Lokalplan (navn, nummer, anvendelse, link), beboerlicens-zone og p-zone.
9. Nærmeste offentlige toilet, legeplads, bibliotek og drikkevandspost, med afstand og
   gangtid.

Brug Københavns Kommunes egne åbne data, hentet live i browseren. Kommunens kortserver
svarer med GeoJSON og tillader cross-origin:
https://wfs-kbhkort.kk.dk/k101/ows?service=WFS&version=1.0.0&request=GetFeature&typeName=k101:<lag>&outputFormat=json&SRSNAME=EPSG:4326&maxFeatures=<n>&bbox=<lonmin>,<latmin>,<lonmax>,<latmax>,EPSG:4326
Hent altid med en lille bbox omkring punktet (f.eks. ±0.003 grader, ca. 300 m; ±0.0004 for
"hvilken flade ligger punktet i"), aldrig hele lag. Serveren forstår også
CQL_FILTER=<felt> ILIKE '<tekst>%'.

Lag og de vigtigste felter (kig selv på de øvrige felter, før du bruger dem):
- Adresser: dar_adgangsadresser, felter adresse ("Molestien 11, 2450 København SV"),
  longitude, latitude. Adressesøgning: CQL_FILTER=adresse ILIKE 'Molest%' med maxFeatures=8,
  vis forslag mens man skriver (debounce).
- Træer: trae_basis (punkter), felter dansk_navn, traeart, planteaar, kategori.
- Tilladelser på gaden: raaden_over_vej_events_anonym_aktuelt (linjer; igangværende og
  kommende), felter sagstype, kategori, lokation, projekt_start, projekt_slut,
  arrangementnavn. Gravearbejder i detaljer: gravetilladelser_aktiv_aabne, felter
  gravetype, bygherre, entreprenoer, tidspunkt_fra, tidspunkt_til, projekt_start, projekt_slut.
- Trafikmeldinger: trafikmelding (linjer), felter haendelsestype, stedfaestelse,
  trafikalt_restriktion, yderligere_information, starttidspunkt, sluttidspunkt.
- Vejstøj: vejstoej_boliger_2022 (punkter pr. bolig), felter vejnavn, husnummer, lden,
  lnight. Match på vejnavn + husnummer hvis muligt, ellers nærmeste punkt.
- Solceller: solcellepotentiale (bygningsflader), felter mwh_pot, mwh_pot_ka, klasse_40,
  arealbyg. Tag den flade, der indeholder punktet (lille bbox, så første/nærmeste flade).
- Skybrud: f2010cds100_1_0 (100-års-regn, 2010) og f2110cds100_1_077 (100-års-regn, 2110),
  flader med feltet gridcode = vanddybdeklasse 0–5, hvor 0 er tørt og højere er dybere. Vis
  klassen som ord (tørt / lidt / en del / meget), ikke som centimeter, medmindre du finder
  klassedefinitionen. Stormflod: hmax263 (100-års-stormflod), samme idé, gridcode 2–10.
- Varme: overfladetemp_gns_seneste_tre_aar (flader), felt overfladetemp (°C).
- Lokalplan: lokalplaner_kk, felter plannavn, plannr, anvendelse, link, lp_status.
- Zoner: beboerzone (zone_navn, beskrivelse, beboerzone_pdf) og parkeringszone (zone_navn,
  kategori, ptakst_dag).
- Nærmeste: toilet_puma_agg_tmf_kk, toilet_puma_agg_andre_kk, legeplads, biblioteker,
  dp_vandinstallationer_p (kun element = Drikkepost). Afstand i luftlinje × 1,3 og gangtid
  ved 5 km/t.

Én HTML-fil, Leaflet fra cdnjs, OpenStreetMap-fliser, ingen backend, ingen nøgler. Dansk
tekst. Design som sunelehmann.com: Fraunces til overskrifter, Inter til brødtekst, den
seksfarvede stribe. Mobil først: adressefelt øverst, kortet under, og et kort pr. emne
nedenunder, der fyldes ud, efterhånden som svarene kommer (vis "henter…", og vis "ingen
inden for 300 m", aldrig en tom boks). Læg adressen i URL'ens hash, så en side kan deles.
Skriv nederst, hvor data kommer fra, hvornår de blev hentet, og at siden er bygget af en
AI-agent under et foredrag.

Byg trinvist, og push efter hvert trin, så siden altid er live med det, der virker: først
adressesøgning + kort + "Hvor er jeg?", så træer og tilladelser, så støj, sol og skybrud, så
resten. Tjek hvert lag mod Molestien 11, 2450 København SV, før du går videre.

Erstat index.html, commit, og push til main. Fortæl mig, når den er live, og hvilke emner
der nåede med.

Arbejd uden at stille spørgsmål undervejs; træf selv de valg, der skal træffes, og nævn dem til sidst.
