# OLLMU: optyczna jednostka modelu językowego

Koncepcja pasywnego rdzenia dyfrakcyjnego (optyczny ROM): wagi zamrożonego operatora liniowego są ośrodkiem, światło liczy MAC w przelocie, a nieliniowość, uwaga i router zostają w elektronice.

Wersja 0.2.0 · październik 2026 · licencja [OLLMU IAL 1.0](LICENSE) (realizacja nieograniczona; zachować publiczną formę autora pomysłu) · pomysł: **Anisul Wałsymzerp** · wersja angielska: [CONCEPT.en.md](CONCEPT.en.md) · cytowania: [REFERENCES.md](REFERENCES.md) · [atrybucja](ATTRIBUTION.md)

**To nie jest działający układ.** To publiczna praca koncepcyjna. Żaden pomiar z tego tekstu nie pochodzi z egzemplarza OLLMU, bo takiego egzemplarza nie ma. Licencja wymaga podania autora. Nie chroni pomysłu i nie jest zgłoszeniem patentowym: publiczne ujawnienie wchodzi w stan techniki.

---

## 1. Streszczenie

Najdroższym składnikiem inferencji dużego modelu językowego, przy dekodowaniu token po tokenie, nie jest mnożenie. Jest nim ponowne odczytanie wag z pamięci przy każdym tokenie. **OLLMU** jest hipotezą, że te wagi da się wypalić w objętościowym rdzeniu dyfrakcyjnym: światło wykonuje liniowe MAC podczas przelotu, a magistrala wag znika, bo wagi są ośrodkiem. Ten sam ruch — złożyć operator z wagami w jednym nośniku, zamiast dociągać wagi do arytmetyki — w cyfrowym krzemie robi m.in. Taalas, zapisując model na sztywno w układzie. OLLMU nie jest tamtym układem i nie dziedziczy jego pomiarów.

Reszta architektury jest tej tezie podporządkowana i jest od niej słabsza. Nieliniowość (ReLU/GELU), uwaga, softmax i router MoE zostają w elektronice. Po każdej optycznej macierzy potrzeba detekcji, przetwornika i ponownej emisji. W fotonice te przetworniki zwykle zjadają zysk energetyczny; §8.8 daje zgrubny bilans, nie pomiar. Router MoE nie powstaje z interferencji w stałym, liniowym ośrodku: wygaszenie portu jest rzutem na stałą podprzestrzeń, a wybór top-k wymaga nieliniowości. Precyzja analogowa rzędu kilku bitów oraz gęstość z §8.9 (górny sufit dyfrakcyjny) nie składają się na model o setkach miliardów parametrów. Wypalony zrzut się starzeje: kryształu nie da się dotrenować, a zrzuty frontier zmieniają się w skali miesięcy.

Laser błękitny GaN oparty na quasi-BIC (**Fakt**: Fąs et al., *Nano Letters*, 2026) jest **kandydatem na źródło spójne**, nie rdzeniem obliczeniowym. Układ z 2026 roku jest pompowany optycznie, ma rozmiar siatki rzędu \(100 \times 100\,\mu\mathrm{m}\) i nie stanowi adresowalnej matrycy.

Pełny transformer w jednym krysztale nie jest twierdzeniem tej pracy. Uczciwy podział: statyczne części liniowe FFN i macierze ekspertów MoE jako optyczny ROM; uwaga \(QK^{\mathsf{T}}\), softmax i wybór eksperta jako elektronika. Pętla autoregresyjna (detekcja → ponowne zakodowanie tokena) jest częścią architektury i **nie jest bezenergetyczna**. Duży klaster cegieł trzyma moc trzema środkami: pasmo C (~1550 nm) w krzemionce, wzmacniacze SOA/EDFA między blokami, regeneracja 3R na SiliconCache — bez udawania, że analogowy SNR jest za darmo.

---

## 2. Jak czytać ten dokument

Każde twierdzenie techniczne nosi jedną etykietę:

| Etykieta | Znaczenie |
| --- | --- |
| **Fakt** | Wynik z cytowanej publikacji |
| **Literatura** | Znana metoda, nie oryginalna dla OLLMU |
| **Hipoteza** | Element proponowanej architektury |
| **Oszacowanie** | Rząd wielkości, nie pomiar |

Jeśli etykiety brakuje przy liczbie, liczba nie jest wynikiem OLLMU.

---

## 3. Wstęp

Przy dekodowaniu autoregresyjnym, mały batch, każdy nowy token czyta w praktyce cały zestaw wag. Intensywność arytmetyczna takiego GEMV jest rzędu jednego MAC na wagę. **Literatura**: energia dostępu do DRAM leży o rzędy wielkości powyżej energii samej operacji arytmetycznej. Horowitz (ISSCC 2014) podaje dostęp do DRAM rzędu 1–2 nJ, a samo I/O powyżej 20 pJ/bit; w obieganej tabeli dla 45 nm odczyt 32-bitowy z DRAM to 640 pJ, mnożenie 8-bitowe to 0,2 pJ. To kotwica dydaktyczna z 2014 roku, nie datasheet HBM z 2026. Wystarcza do tezy strukturalnej: dopóki \(b \cdot e_{\mathrm{bit}} \gg e_{\mathrm{MAC}}\), rachunek jest tanim dodatkiem do wożenia wag. **Literatura (kontekst przemysłowy)**: Taalas składa ten sam wniosek w krzemie — wagi modelu są zapisane na sztywno w układzie, inferencja nie wozi ich z pamięci poza chipem, a mały SRAM zostaje na KV-cache i LoRA. Publicznie: Llama 3.1 8B i rząd dwóch miesięcy od otrzymanego modelu do sprzętu ([taalas.com](https://taalas.com/the-path-to-ubiquitous-ai/), sprawdzone 2026-10-21). To nie jest fotonika i nie jest dowodem OLLMU.

**Literatura**: fotoniczne MAC skalują się korzystnie przy dużym \(N\) i niskiej precyzji analogowej, rzędu kilku bitów, a nie FP16 (Nahmias et al., 2020). **Literatura**: sieci dyfrakcyjne D²NN wykonują inferencję pasywnie po cyfrowym treningu (Lin, Ozcan et al., 2018). **Literatura**: siatki interferometrów Macha–Zehndera realizują mnożenie macierzy na chipie krzemowym (Shen et al., 2017). Żadna z tych linii nie dała dotychczas LLM w krysztale.

Luka, którą OLLMU próbuje nazwać, a nie zamknąć: które **zamrożone** operatory liniowe warto wypalić, skoro (i) zysk istnieje tylko wtedy, gdy zniknięcie magistrali wag przeżyje koszt światło–prąd–światło, (ii) precyzja analogowa jest rzędu kilku bitów, (iii) zrzutu nie da się dotrenować, gdy model się zestarzeje. Krótki light cache i elektronika poza kryształem są dodatkami do tej tezy, nie jej substytutem.

---

## 4. Fakt: laser quasi-BIC GaN

**Fakt.** Fąs, Pruszyńska-Karbownik, Sawicka, Yavorskiy, Feduniewicz, Gołyga, Słowikowski, Kacperski, Siekacz, Muzioł, Nowicki, Kazakov, Wróbel, Czyszanowski i Suffczyński wykazali lasing w zakresie 440–460 nm w temperaturze pokojowej. Wzmocnienie dają studnie kwantowe InGaN sprzężone z quasi-BIC w monolitycznej siatce podfalowej GaN. Pod strukturą leży warstwa nanoporowatego GaN z trawienia elektrochemicznego, która tłumi wyciek modów do podłoża. DOI: [10.1021/acs.nanolett.6c01930](https://doi.org/10.1021/acs.nanolett.6c01930) ([ACS](https://pubs.acs.org/doi/10.1021/acs.nanolett.6c01930), [PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC13397883/)).

Parametry z tej samej pracy:

- próg \(0{,}16\,\mathrm{mJ\,cm^{-2}}\) na impuls,
- szerokość linii poniżej \(0{,}04\,\mathrm{nm}\),
- emisja silnie spolaryzowana liniowo; stosunek TE/TM równy 52,
- siatki rzędu \(100 \times 100\,\mu\mathrm{m}\),
- projekt numeryczny (PWAM), fabrykacja e-beam + RIE + wygładzanie TMAH,
- **pompowanie optyczne**, nie elektryczne.

**Fakt, ograniczenie.** Praca nie opisuje matrycy niezależnie sterowanych pikseli laserowych, nie podaje sprawności ściany wtyczkowej elektrycznej i nie dotyczy sieci neuronowych.

**Hipoteza (zastosowanie w OLLMU).** Jeśli w przyszłości powstanie elektrycznie pompowana, adresowalna matryca o podobnej spójności i polaryzacji, nadaje się na interfejs wejściowy. Silna polaryzacja TE jest zaletą w rachunku interferencyjnym. Długość fali ~450 nm **nie** nadaje się do litografii zaawansowanych chipów; limit dyfrakcyjny jest o rząd za gruby względem EUV 13,5 nm. OLLMU nie jest projektem litograficznym.

---

## 5. Literatura, na której stoi reszta

### 5.1. D²NN

**Literatura.** Lin, Ozcan et al. (2018) trenują cyfrowo współczynniki transmisji warstw, potem fizycznie je realizują. Inferencja to dyfrakcja. Demonstracje: klasyfikacja (w tym THz, druk 3D), nie modele językowe.

Pole na warstwie \(\ell\) wiąże się z następną przez propagator dyfrakcyjny \(D_\ell\) i lokalny, wyuczony transmitancję \(T_\ell\):

\[
U_{\ell+1} = D_\ell \bigl( T_\ell \odot U_\ell \bigr).
\]

Po fabrykacji \(T_\ell\) jest stałe: to optyczny ROM.

### 5.2. Fotoniczne MAC i przemysł

**Literatura.** Shen et al. (2017): programowalna siatka MZI, rozpoznawanie samogłosek. Nahmias et al. (2020): przewaga energetyczna fotoniki przy \(N \gtrsim 500\), rozmiarze procesora \(\gtrsim 100\,\mu\mathrm{m}\) i precyzji szumowej \(\lesssim 4\) bity. Lightmatter i pokrewne firmy budują **hybrydy** elektroniczno-fotoniczne; to kontekst przemysłowy, nie dowód OLLMU.

### 5.3. Wolne światło i Kerr

**Literatura.** W krysztale fotonicznym prędkość grupowa spada: \(v_g = c / n_g\). Baba (2008) podsumowuje, że duże \(n_g\) zwiększa interakcję światło–materia i obniża próg nieliniowości, kosztem pasma (produkt pasmo–opóźnienie). Kerr:

\[
n(I) = n_0 + n_2 I.
\]

Przesunięcie fazy na długości efektywnej \(L_{\mathrm{eff}}\):

\[
\Delta\varphi = \frac{2\pi}{\lambda}\, n_2 I\, L_{\mathrm{eff}}.
\]

Slow light powiększa \(L_{\mathrm{eff}}\) i lokalne \(I\). Demonstracje aktywacji nieliniowych w fotonice (m.in. jamy PhC, Liu et al., 2026) są w skali femtodżuli / pikosekund, nie w skali LLM.

Efekt Kerra elektronowego jest szybki i słaby; efekt cieplny i dyspersja swobodnych nośników są silniejsze i wolniejsze. **Hipoteza OLLMU nie może zakładać, że Kerr sam z siebie jest ReLU.**

### 5.4. Pamięć jako opóźnienie

**Literatura.** Optyczne przetwarzanie rezerwuarowe i linie opóźniające trzymają stan jako fotony w drodze. To nie jest DRAM. Pojemność i czas życia ogranicza strata i dekoherencja.

### 5.5. Zapis 3D w szkle

**Literatura.** Femtosekundowa zmiana współczynnika załamania w szkle istnieje od 1996 (Davis et al.). Chip 3D PNN zapisany fs-laserem (2026) klasyfikuje MNIST. Kontrast \(\Delta n\) jest zwykle \(10^{-3}\)–\(10^{-2}\), nie dowolny. To nie jest skala miliardów parametrów.

### 5.6. Materiał na ~450 nm

**Fakt materiałowy.** Krzem w paśmie błękitnym silnie absorbuje (Green, 2008). Prowadzenie: Si₃N₄ (m.in. ~0,08 dB/cm przy 450 nm, Chauhan et al., 2020), GaN, LiNbO₃. Hybrydowe wiązanie diod InGaN do platformy SiN jest literaturą (Mu et al., 2026), nie produkcją matryc BIC.

### 5.7. Wagi, które stoją w miejscu

**Literatura.** Horowitz (ISSCC 2014), jak w §3: wożenie bitu z DRAM kosztuje wielokrotnie więcej niż wąski MAC. **Literatura (kontekst przemysłowy).** Taalas realizuje wniosek w cyfrowym krzemie: model jest częścią układu, a nie zawartością HBM. Elastyczność, którą firma zostawia, siedzi obok zamrożonych wag — okno kontekstu i LoRA w SRAM — a nie w przelutowaniu wag bazowych. OLLMU przenosi samą ideę „wagi są nośnikiem” do szkła. Nie przenosi ani gęstości maski CMOS, ani ścieżki LoRA, ani dwumiesięcznego cyklu od zrzutu do karty.

---

## 6. Hipoteza: architektura OLLMU

```mermaid
flowchart LR
  tokens[TokenEncoder] --> router[ElectronicRouter]
  router --> emitters[EmitterArray]
  emitters --> core[DiffractiveROM]
  core --> ports[OutputPorts]
  ports --> lightCache[LightCache]
  lightCache --> core
  ports --> siCache[SiliconCache]
  siCache --> emitters
  ports --> ctx[ContextMemory]
  ctx --> emitters
  ports --> recv[ReceiverArray]
  recv --> recode[AutoregressiveRecode]
  recode --> emitters
```

Porty są drogami optycznymi. Który ekspert świeci, gdzie włączyć nieliniowość i kiedy sięgnąć po kontekst, decyduje router elektroniczny. Faza zapisana w szkle tej decyzji nie podejmuje.

**Hipoteza.** Przetwarzanie liniowe, zamrożone, siedzi w świetle. Decyzje zależne od wejścia siedzą w elektronice. Pamięć ma cztery skale czasu.

1. **Koder tokenów (hybryda).** Wektor aktywacji jest mapowany na amplitudy i fazy. Na starcie: elektronika + modulatory.

2. **Router (elektronika).** Mała sieć: logits, softmax, top-k. Wybiera, który rdzeń eksperta oświetlić, i czy w tym takcie wołać SiliconCache albo pamięć kontekstu. Wagi routera są małe i zostają w krzemie, więc da się je wymienić bez nowego kryształu.

3. **Matryca emisyjna.** Spójne źródła. Kandydat: matryca BIC GaN. Dziś: optycznie pompowane siatki. Router włącza emisję do wybranego rdzenia; to przełącznik elektroniczny, nie interferencja w szkle.

4. **Rdzeń dyfrakcyjny (ROM) — processing.** Zapisane \(n(\mathbf{r})\). Światło liczy stały operator \(W\). Naturalny ładunek: liniowa część FFN i macierz wybranego eksperta. Przelot przez ośrodek *już* jest opóźnieniem \(\tau_{\mathrm{core}} = n L / c\); to nie jest wada, tylko zegar optyczny.

5. **Light cache (w krysztale / na krawędzi optycznej).** Tam, gdzie przyda się *lekkie* opóźnienie — zsynchronizowanie dwóch przebiegów, echo warstwy, złożenie fazy — pole idzie w wolniejszy ośrodek (większe \(n\) albo \(n_g\): wolne światło, inny kryształ, dłuższa ścieżka). To **nie** zastępuje KV-cache ani ReLU. Kerr na tej ścieżce jest opcją, nie wymogiem. Wejście na tę ścieżkę jest zaprojektowanym sprzęgłem albo przełącznikiem, nie decyzją liniowego ośrodka.

6. **SiliconCache (poza kryształem).** Detekcja → przetwornik analogowo-cyfrowy → nieliniowość (ReLU/GELU/mały MLP) i/lub wektor thinking. Tu też ląduje router, jeśli nie wydzielono go obok. Wynik wraca na matrycę emisyjną. Każde takie przejście jest światło–prąd–światło i wchodzi do bilansu z §8.8.

7. **Pamięć kontekstu (poza kryształem).** Długi stan (odpowiednik KV-cache, scratchpad, historia tokenów). SRAM/DRAM albo inny bufor elektroniczny. Światło tam nie mieszka.

8. **Matryca odbiorcza i pętla recode.** Kolejny token. **Nie jest za darmo.**

### 6.1. Cztery skale opóźnienia

| Skala | Gdzie | Co trzyma | Rząd czasu (**Oszacowanie**) |
| --- | --- | --- | --- |
| Przelot rdzenia | w krysztale | nic poza falą w locie | ps–ns na centymetr |
| Light cache | wolniejszy ośrodek optyczny | krótkie echo, faza | ps–ns, produkt pasmo–opóźnienie |
| SiliconCache | krzem poza kryształem | nieliniowość, router, thinking, alignment taktów | ns–µs |
| Context memory | krzem poza kryształem | kontekst rozmowy | µs–ms i dłużej, pojemność cyfrowa |

**Literatura.** Różne \(n\) i \(n_g\) zmieniają czas przelotu; to standard optyki, nie nowy efekt. **Hipoteza** dotyczy tylko *użytku*: krótkie echo optyczne jest zegarem i złożeniem fazy. Długi stan i decyzja „który ekspert” są elektroniczne.

### 6.2. Czego interferencja nie wybierze

**Hipoteza, fizyka.** Rdzeń o ustalonym \(n(\mathbf{r})\) jest operatorem liniowym, \( \mathbf{u}_{\mathrm{out}} = W \mathbf{u}_{\mathrm{in}} \), z jednym \(W\) dla wszystkich wejść. Moc na porcie \(i\) to

\[
P_i = \lvert \mathbf{w}_i^{\dagger} \mathbf{u}_{\mathrm{in}} \rvert^2.
\]

To stała forma kwadratowa. Zbiór wejść, które gaszą port, jest jądrem funkcjonału \(\mathbf{w}_i\): stałą podprzestrzenią, wybraną przy zapisie, niezależną od porównania „który ekspert jest większy”.

Dwa ramiona o fabrykowanej fazie \(\theta\) nie zmieniają werdyktu. Jeśli \(\theta\) gasi port dla każdego wejścia, wiersz \(W\) jest zerem i port jest martwy. Jeśli moc zależy od wejścia, nadal jest to miękkie dopasowanie do jednego, z góry wybranego kierunku, a nie top-k.

Router MoE (**Literatura**: Fedus et al.) liczy

\[
r(\mathbf{u}) = \mathrm{TopK}\bigl(\mathrm{softmax}(W_r \mathbf{u})\bigr).
\]

\(\mathrm{softmax}\) i \(\mathrm{TopK}\) są nieliniowe. \(W_r\) jest małe i zostaje w elektronice. Wybrany ekspert jest potem jednym ze stałych operatorów optycznych \(W^{(e)}\). Oświetlenie tylko tego eksperta jest włączeniem modulatora albo wyborem cegły, czyli znowu elektroniką.

Wersje wcześniejsze tego tekstu opisywały gaszenie portu fazą \(\theta = \pi\) jako politykę pamięci uczoną w geometrii. To była pomyłka: stały ośrodek nie podejmuje decyzji zależnej od wejścia. Zostaje rzecz węższa i prawdziwa. Szkło trzyma duże \(W\). Krzem decyduje, które \(W\) w tym takcie pracuje.

### 6.3. Czego rdzeń nie robi

Rdzeń nie trenuje się w locie, nie jest DRAM-em, nie trzyma 128k tokenów i nie wybiera ekspertów. OLLMU jest nośnikiem *jednego* zrzutu operatorów liniowych. Długi kontekst, nieliniowość i router są kontraktem z elektroniką. Elektronikę da się później poprawić. \(n(\mathbf{r})\) da się tylko wymienić razem z kryształem.

### 6.4. Wygaszenie sygnału w dużym klastrze

Duży OLLMU to szereg cegieł (ekspert → ekspert), nie jedna bryła. Światło słabnie. Trzy środki — z etykietami, bez reklamy.

```mermaid
flowchart LR
  e1[ExpertROM_1] --> amp[SOA_or_EDFA]
  amp --> e2[ExpertROM_2]
  e1 --> si[SiliconCache_3R]
  si --> e2
```

**A. Pasmo C, ~1550 nm, krzemionka.** **Literatura:** w czystym rdzeniu krzemionkowym strata rekordowa to 0,1424 dB/km przy 1550 nm (Tamura et al., *JLT* 2018, DOI [10.1109/JLT.2018.2796647](https://doi.org/10.1109/JLT.2018.2796647)). **Oszacowanie** na 30 cm czystego włókna: \(\sim 4\times 10^{-5}\,\mathrm{dB}\) — margines. **To nie jest strata ROM-u.** Wypalony \(\Delta n\), rozpraszanie wokseli, dyfrakcja i defekty fs-lasera dominują nad absorpcją objętościową. Rayleigh \(\propto \lambda^{-4}\): przejście 450 → 1550 nm obniża ten składnik o czynnik \(\sim 140\). **Hipoteza skalowania:** małe chipy / interfejs BIC mogą zostać przy błękicie; **duże cegły klastra** idą na 1550 nm (fused silica). Krzem jako *falowód* jest wtedy przezroczysty; fotodioda przy 1550 nm to Ge lub InGaAs, nie czysty Si.

**B. Wzmacniacze optyczne między cegłami (SOA, EDFA).** **Literatura:** EDFA i SOA podbijają moc bez detekcji. **Hipoteza:** po ekspercie 1 słabe pole wlatuje w wzmacniacz pompowany tanim laserem i wchodzi do eksperta 2 nadal jako światło. **Nie twierdzimy**, że faza i analogowa amplituda wychodzą nietknięte. ASE dokłada szum (współczynnik szumu wzmacniacza, rząd kilku dB); SOA ma chirp (czynnik Henry'ego) i saturację, która zgniata precyzję MAC. To stacja mocy, nie magiczne kopiowanie fotonów. Kaskada wzmacniaczy **bez** zejścia do elektroniki kumuluje ten szum; patrz §8.7.

**C. Regeneracja 3R na SiliconCache.** **Hipoteza, najuczciwsza na analog.** Słabe światło pada na detektor, krzem robi nieliniowość (ReLU itd.), nowy laser strzela w kolejny rdzeń impulsem o pełnej mocy. To klasyczna regeneracja: reamplify / reshape (i ewentualnie retime). Resetuje SNR. Płaci O-E-O: czas, energia, utrata fazy, jeśli nie odtwarzasz I/Q. W architekturze OLLMU ta bocznica *już istnieje* — tu jest też stacją przepompowującą, nie tylko nieliniowością. Reset SNR i koszt przetwornika są tym samym zdarzeniem. Nie da się mieć długiego łańcucha analogowego bez kumulacji błędu i jednocześnie nie płacić za 3R.

**Kolejność użycia (**Hipoteza**):** light cache i 1550 nm tam, gdzie strata jest mała i faza ma zostać; SOA/EDFA między cegłami, dopóki analog jeszcze trzyma; SiliconCache, gdy trzeba nieliniowości, routera albo gdy analogowy SNR padł. Harmonogram jest projektem systemu. Nie jest fazą nauczoną w \(n(\mathbf{r})\).

---

## 7. Mapowanie na transformer (uczciwy podział)

**Literatura.** Warstwa transformera (Vaswani et al., 2017) to uwaga plus FFN. FFN to zwykle dwie stałe macierze i nieliniowość. MoE (Fedus et al.) wybiera podzbiór ekspertów nieliniowym routerem.

| Blok | Czy ROM optyczny wystarczy? | Propozycja OLLMU |
| --- | --- | --- |
| Embedding / unembedding | Częściowo (stała macierz) | ROM albo elektronika |
| FFN, część liniowa \(W_1, W_2\) | Tak, jako zamrożone \(W\) | Główny processing w świetle; wagi się nie ruszają |
| Nieliniowość FFN | Nie | SiliconCache; każde przejście to O-E-O |
| Macierz eksperta MoE | Tak, każda z osobna | Osobny ROM na eksperta |
| Router MoE, top-k | Nie | Elektronika; interferencja nie jest routerem (§6.2) |
| Uwaga \(QK^{\mathsf{T}}\) | Nie: macierz zależy od tokenów | Hybryda; stan w context memory |
| Softmax | Nie | SiliconCache |
| KV-cache / kontekst | Nie jako objętość optyczna | Context memory poza kryształem |
| Krótkie echo / faza | Tak, jeśli \(\tau\) małe | Light cache (wolniejszy ośrodek) |
| Autoregresja | Nie w jednym przelocie | Pętla recode |

**Hipoteza operacyjna.** Szkło = stałe \(W\), odczytywane światłem zamiast z HBM. Krzem = wszystko, co zależy od bieżącego wejścia albo co ma się dać wymienić bez nowego kryształu: \(\varphi\), router, uwaga, kontekst, ewentualna poprawka niskiego rzędu. Light cache = krótkie optyczne opóźnienie, nie pamięć rozmowy.

---

## 8. Matematyka

Równania poniżej są standardowe. Nie stanowią dowodu, że OLLMU skaluje się do setek miliardów parametrów. Nie są też pomiarem energii na token.

### 8.1. Propagacja

**Literatura.** Między płaszczyznami \(z_\ell\) i \(z_{\ell+1}\) pole w przybliżeniu skalarnego widma kątowego:

\[
\tilde{U}(k_x, k_y; z_{\ell+1}) = \tilde{U}(k_x, k_y; z_\ell)\, \exp\bigl(i z_{\Delta} \sqrt{k_0^2 n^2 - k_x^2 - k_y^2}\bigr),
\]

gdzie \(z_{\Delta} = z_{\ell+1}-z_\ell\), \(k_0 = 2\pi/\lambda\). Na płaszczyźnie warstwy działa punktowo \(T_\ell(x,y)\). W objętości z ciągłym \(n(\mathbf{r})\) trzeba Maxwella albo przybliżenia eikonalnego / BPM. Trening wymaga **różniczkowalnego bliźniaka**; pełny FDTD na każdą epokę LLM jest niepraktyczny (**Oszacowanie** kosztu, nie pomiar).

### 8.2. Operator liniowy

Dla ustalonej długości fali i polaryzacji rdzeń realizuje

\[
\mathbf{u}_{\mathrm{out}} = W \mathbf{u}_{\mathrm{in}},
\]

gdzie \(W \in \mathbb{C}^{M \times N}\) wynika z \(n(\mathbf{r})\). Wagi cyfrowego FFN trzeba wcisnąć w fizycznie dopuszczalne \(W\) (straty, sprzężenie, zakres \(\Delta n\)). To problem odwrotny, nie kopiowanie macierzy PyTorch do szkła.

### 8.3. Opóźnienie: rdzeń vs light cache vs krzem

Przelot przez rdzeń:

\[
\tau_{\mathrm{core}} = \frac{n L}{c}.
\]

Light cache w wolniejszym ośrodku:

\[
\tau_{\mathrm{lc}} = \frac{n_g L_{\mathrm{lc}}}{c}.
\]

**Literatura.** Nie można jednocześnie mieć dowolnie dużego \(n_g\) i szerokiego pasma. **Oszacowanie:** \(\tau_{\mathrm{core}}\) i \(\tau_{\mathrm{lc}}\) to ps–ns — echo i zegar optyczny, nie kontekst 128k. SiliconCache i context memory nie podlegają produktowi pasmo–opóźnienie; ich cena to O-E-O i DRAM.

### 8.4. Kerr na light cache (opcja)

\[
\Delta\varphi = \frac{2\pi}{\lambda}\, n_2 I\, L_{\mathrm{eff}}(n_g).
\]

Wolniejsze światło podnosi lokalne \(I\) i \(L_{\mathrm{eff}}\). To może być efekt uboczny light cache. **Nie** jest to domyślna nieliniowość modelu. ReLU/GELU siedzą w SiliconCache. Kerr ≠ ReLU, chyba że ktoś to zmierzy.

### 8.5. Port jako forma kwadratowa

Niech wiersz portu będzie ustalonym \(\mathbf{w}\). Wtedy \(P = \lvert \mathbf{w}^{\dagger} \mathbf{u} \rvert^2\) zależy od \(\mathbf{u}\) tylko o tyle, o ile \(\mathbf{u}\) ma składową wzdłuż \(\mathbf{w}\). Trening przed zapisem może dobrać \(\mathbf{w}\) (jaki kierunek w ogóle sprzęga się do portu). Nie może sprawić, by ten sam port raz był ekspertem 3, a raz ekspertem 7, zależnie od tokena. Tę drugą rzecz robi router \(W_r\) z §6.2 i zostaje poza szkłem.

### 8.6. Funkcja straty

**Hipoteza.** \(\mathcal{L}_{\mathrm{task}}\) i kara za wołanie elektroniki są liczone na cyfrowym bliźniaku. W szkle ląduje tylko \(n(\mathbf{r})\) odpowiadające wybranym \(W\). Wagi routera, skalowanie nieliniowości i ewentualna poprawka niskiego rzędu zostają w opisie SiliconCache i wolno je zmienić po zapisie.

\[
\mathcal{L} = \mathcal{L}_{\mathrm{task}} + \lambda_{\mathrm{si}} \mathbb{E}[C_{\mathrm{Si}}] + \lambda_{\mathrm{ctx}} \mathbb{E}[C_{\mathrm{ctx}}] + \lambda_{\mathrm{ph}} \mathcal{L}_{\mathrm{physics}} + \lambda_f \mathcal{L}_{\mathrm{fab}}.
\]

\(C_{\mathrm{Si}}\) i \(C_{\mathrm{ctx}}\) są kosztami rzeczywistych wywołań elektronicznych (przetwornik, router, odczyt kontekstu), nie mocą optyczną na porcie udającą bit decyzyjny. Bez bliźniaka, który ma szum, O-E-O i osobny router, „wypalenie modelu w szkle” jest hasłem.

### 8.7. Precyzja analogowa i kumulacja

**Literatura / Oszacowanie.** Przy szumie addytywnym

\[
b \approx \tfrac12 \log_2 (1 + \mathrm{SNR}).
\]

Szum śrutowy, dryf fazy, rozpraszanie i błąd zapisu ograniczają \(b\) do **kilku bitów**, zgodnie z Nahmias et al. (próg ~4 bity w ich porównaniu). Dla \(b = 4\) wzór daje \(\mathrm{SNR} \approx 255\) (ok. 24 dB): błąd rms rzędu kilku procent pełnej skali, zanim doliczyć błąd zapisu i dryf. Modele cyfrowe w INT4/INT8 pokazują, że niska precyzja bywa wystarczająca w FFN — ale to arytmetyka ze skalą i dokładną akumulacją, nie łańcuch analogowy.

**Oszacowanie, kumulacja.** Są tu trzy różne mechanizmy i nie wolno ich sklejać w jedno zdanie.

- Wewnątrz jednej optycznej macierzy budżet to te kilka bitów. Tyle zostaje po zejściu na detektor, nawet jeśli poprzedni stopień przeszedł przez 3R.
- Kaskada analogowa bez zejścia do elektroniki (cegła, SOA, cegła, …) dokłada szum wzmacniacza i błąd fazy na każdym stopniu. Nieskorelowany błąd względny rośnie jak \(\sqrt{L}\); błąd systematyczny \(\Delta n\) nie uśrednia się wcale.
- Zejście na SiliconCache i ponowna emisja **resetują** SNR i **są** kosztem \(e_{\mathrm{conv}}\) z §8.8. Długa droga analogowa bez kumulacji oraz droga bez przetworników nie są jednocześnie dostępne.

Osobno: błąd zapisu \(\Delta n(\mathbf{r})\) jest systematyczny. Głośniejszy laser go nie kasuje. Ta praca nie pokazuje, że łańcuch o budżecie ~4 bitów na macierz, po dziesiątkach warstw i po realnym błędzie zapisu, zostaje w basenie jakości cyfrowego FFN w INT4.

### 8.8. Energia na token

Równanie jest rozbiciem, nie wynikiem:

\[
E_{\mathrm{token}} = E_{\mathrm{src}} + N_{\mathrm{conv}}\, e_{\mathrm{conv}} + E_{\mathrm{ctx}} + E_{\mathrm{recode}} + E_{\mathrm{attn, hyb}}.
\]

Dla zamrożonego \(W\) składnik „odczyt wag z pamięci” jest zerem. Stratę optyczną i tak płaci źródło, więc siedzi w \(E_{\mathrm{src}}\). **Fakt metodologiczny.** Zero dżuli na magistrali wag dotyczy wyłącznie tego, że \(W\) nie jedzie z HBM. Nie dotyczy tokena.

Po stronie dekodowania na GPU, jedna warstwa, batch 1, wagi nie mieszczą się w pamięci przy arytmetyce:

\[
E_{\mathrm{move}} = N_w \, b \, e_{\mathrm{bit}}, \qquad E_{\mathrm{MAC}} = N_w \, e_{\mathrm{MAC}}, \qquad \frac{E_{\mathrm{move}}}{E_{\mathrm{MAC}}} = \frac{b \, e_{\mathrm{bit}}}{e_{\mathrm{MAC}}}.
\]

\(N_w = 2\, d\, d_{\mathrm{ff}}\) dla dwóch macierzy FFN. Stosunek nie zależy od szerokości. Na kotwicy Horowitza (\(b = 4\), \(e_{\mathrm{bit}} = 20\,\mathrm{pJ}\), \(e_{\mathrm{MAC}} = 0{,}2\,\mathrm{pJ}\)) wynosi 400. Sonda „a jeśli bit jest dwadzieścia razy tańszy”, \(e_{\mathrm{bit}} = 1\,\mathrm{pJ}\), daje stosunek 20. Sonda nie jest pomiarem HBM. Pokazuje, gdzie teza pęka: przy \(e_{\mathrm{bit}}\) rzędu \(e_{\mathrm{MAC}} / b\), czyli przy ułamku pikodżula na bit dla tej kotwicy MAC, mnożenie dogania transport. Poniżej tej okolicy główna teza tej pracy traci siłę nawet przed doliczeniem laserów.

Po stronie OLLMU ta sama warstwa płaci przetwornikami. Liczymy cztery zdarzenia obciążające tę warstwę: emisja wejścia (\(d\)), detekcja po \(W_1\) (\(d_{\mathrm{ff}}\)), emisja w \(W_2\) (\(d_{\mathrm{ff}}\)), detekcja po \(W_2\) (\(d\)).

\[
N_{\mathrm{conv}} = 2\,(d + d_{\mathrm{ff}}).
\]

W stosie sąsiednie warstwy dzielą granicę (detekcja jednej jest emisją następnej), więc głęboki stos płaci mniej zdarzeń na warstwę, a budżet na jedno zdarzenie rośnie. Poniższy próg jest surowszy: każe tej warstwie zapłacić wejście i wyjście w całości.

Próg, przy którym same przetworniki doganiają sam transport wag:

\[
e_{\mathrm{conv}}^{\star} = \frac{d\, d_{\mathrm{ff}}}{d + d_{\mathrm{ff}}}\, b\, e_{\mathrm{bit}}.
\]

**Oszacowanie, nie pomiar.** Przykład szerokości w stylu warstwy 7B (\(d = 4096\), \(d_{\mathrm{ff}} = 11008\)) oraz warstwy zabawkowej fazy 1 (\(d = 256\), \(d_{\mathrm{ff}} = 1024\)):

| Założenie | \(e_{\mathrm{bit}}\) | \(E_{\mathrm{move}}/E_{\mathrm{MAC}}\) | \(e_{\mathrm{conv}}^{\star}\) przy \(d=4096\) | \(e_{\mathrm{conv}}^{\star}\) przy \(d=256\) |
| --- | --- | --- | --- | --- |
| I/O DRAM, Horowitz 2014 | 20 pJ | 400 | ~240 nJ | ~16 nJ |
| Sonda: bit 20× tańszy | 1 pJ | 20 | ~12 nJ | ~0,8 nJ |

Szeroka warstwa daje przetwornikowi budżet setek nanodżuli na kotwicy z 2014 roku i około dziesięciu nanodżuli, jeśli bit staniał dwudziestokrotnie. Wąska warstwa fazy 1 jest dużo mniej wyrozumiała. \(E_{\mathrm{src}}\) stoi **poza** tą tabelą i potrafi ją unieważnić. Tożsamość do podstawienia, nie pomiar lasera: \(1\,\mathrm{mW}\times 1\,\mu\mathrm{s} = 1\,\mathrm{nJ}\). Źródło 1 W przez 100 ns to już 100 nJ na impuls, czyli rząd progu szerokiej warstwy przy 20 pJ/bit i wyraźnie powyżej progu przy 1 pJ/bit.

Czego ta tabela nie robi: nie wolno pomnożyć 20 pJ/bit przez liczbę parametrów modelu frontier i nazwać iloczynu energią GPU. Kotwica służy do stosunku i do progu \(e_{\mathrm{conv}}^{\star}\). Werdykt „szkło bije GPU” wymaga zmierzonych \(e_{\mathrm{conv}}\) i \(E_{\mathrm{src}}\). Ta praca ich nie ma. Ma je zebrać faza 1.

### 8.9. Objętość, parametry, sufit

**Oszacowanie, nie pomiar.** Niech wokseli o rozmiarze \(\sim\lambda\) w szkle niosą po \(k\) bitów analogowych efektywnych. Dla \(\lambda = 450\,\mathrm{nm}\) i \(k = 4\):

\[
\rho \sim \frac{k}{\lambda^3} \approx \frac{4}{(4{,}5\times 10^{-7})^3}\ \mathrm{bit/m^3} \sim 4\times 10^{19}\ \mathrm{bit/m^3}.
\]

To **górny sufit dyfrakcyjny**, nie gęstość użyteczna. Sprzężenia krzyżowe, prowadnice, porty i tolerancje obcinają rzędy wielkości. Nawet wtedy mapa „parametr PyTorch = woksel” jest fałszywa: \(W\) jest gęstym operatorem falowym, nie CSR macierzą. Woksel rozmiaru \(\lambda\) nie trzyma 4 bitów z cyfrowym marginesem szumu.

Objętość przy tym suficie, \(V = N b / \rho\), dla \(b = 4\):

| Parametry \(N\) | Objętość na suficie |
| --- | --- |
| \(10^{11}\) | \(\sim 10\,\mathrm{cm}^3\) |
| \(10^{12}\) | \(\sim 0{,}1\,\mathrm{dm}^3\) |

Modele frontier są tego rzędu i większe. Sufit wygląda skromnie tylko dlatego, że jest sufitem. Trzy rzędy w dół na sprzężeniach to litry; sześć rzędów to metry sześcienne. Do tego dochodzi §8.7: nawet gdyby objętość się zgadzała, budżet kilku bitów na macierz przez dziesiątki warstw nie jest pokazany jako wystarczający. Wniosek operacyjny tej pracy jest węższy niż „cały model w krysztale”: jedna liniowa warstwa FFN, potem cegła ekspertów, jeśli fazy 1–4 w ogóle przejdą.

---

## 9. Trening przed zapisem w krysztale

**Hipoteza twarda.** Operatory \(W\), które mają siedzieć w szkle, uczą się na cyfrowym bliźniaku **zanim** \(n(\mathbf{r})\) zostanie wypalone. Po zapisie kryształ jest ROM tych operatorów. Nie ma dotrenowania \(n(\mathbf{r})\).

Kolejność:

1. Bliźniak: propagator rdzenia + light cache (\(n\), \(n_g\)) + modele detektora, ADC, nieliniowości, routera i context memory, ze szumem na poziomie zadeklarowanych bitów.
2. Trening \(\mathcal{L}\) z §8.6. Router uczy się *kiedy* wołać krzem i *który* ekspert oświetlić. Te wagi nie idą do szkła.
3. Kwantyzacja \(n(\mathbf{r})\) do tego, co umie fs-zapis / litografia.
4. Zapis w krysztale. Koniec uczenia wag optycznych.
5. Kalibracja elektroniki (progi, delay, wagi routera). To nie jest przetrenowanie kryształu.

Nowy model bazowy oznacza nowy kryształ. Poprawka, która mieści się w routerze albo w małej poprawce niskiego rzędu po stronie krzemu, kryształu nie rusza — i właśnie dlatego nie ratuje zrzutu, którego trzeba wymienić w całości. Taalas zostawia sobie na taki margines SRAM i cykl maski rzędu miesięcy. OLLMU tego SRAM-u w objętości szkła nie ma.

**Oszacowanie żywotności.** Zrzuty frontier wymienia się w skali miesięcy. GPU płaci magistralę wag i tego samego dnia ładuje następny checkpoint. Kryształ magistralę oszczędza i następnego checkpointu nie przyjmie. Cegła jest racjonalna tylko wtedy, gdy użyteczne życie zrzutu jest dłuższe niż czas zapisu, a zaoszczędzona energia transportu zdąży oddać koszt fabrykacji. Ta praca tej nierówności nie pokazuje. Zamrożenie, które przy GPU jest ceną elastyczności, tutaj jest ostrzejsze: nośnik umiera razem z modelem.

---

## 10. Materiały i fabrykacja

| Rola | Kandydat | Uwaga |
| --- | --- | --- |
| Źródło | Quasi-BIC GaN (Fąs 2026) | Dziś optycznie pompowane |
| Prowadzenie widzialne | Si₃N₄, GaN, LN | Nie krzem SOI |
| Źródło (duża cegła) | DFB / InP, ~1550 nm | Pasmo C; dojrzalsze niż matryca BIC |
| Objętość ROM (klaster) | Fused silica, zapis 1550 nm | Absorpcja włókna ≠ strata ROM |
| Wzmacniacz między cegłami | SOA, EDFA | ASE, chirp, saturacja |
| Detekcja przy 1550 nm | Ge / InGaAs | Nie czysty krzem |
| Light cache | Wolniejszy ośrodek, PhC, dłuższa ścieżka | Krótkie echo, nie KV-cache |
| SiliconCache | CMOS poza kryształem | Nieliniowość, router, thinking, takt |
| Context memory | SRAM/DRAM poza kryształem | Kontekst; da się wymienić bez nowego szkła |
| Detekcja | Matryca Si / GaN | Faza wymaga interferometru lokalnego |

Niebieskie światło: mniejsza \(\lambda\) zagęszcza woksele, Rayleigh \(\propto \lambda^{-4}\). **Hipoteza skalowania (§6.4):** BIC GaN jako kandydat na mały interfejs; duże cegły na 1550 nm. Detektor Si nie widzi 1550 nm.

---

## 11. Model błędu

Źródła błędu, które dokumentacja uznaje z góry:

- niedokładność \(\Delta n(\mathbf{r})\) przy zapisie (składnik systematyczny, §8.7),
- chropowatość i strata,
- dryf termiczny fazy (szczególnie Kerr cieplny),
- wibracje i niestabilność interferometrów,
- szum śrutowy i ADC,
- ASE i saturacja SOA/EDFA w kaskadzie bez 3R,
- fotodioda przy 1550 nm (Ge/InGaAs, nie Si),
- niezgodność bliźniaka z Maxwelllem,
- starzenie źródła i fotociemnienie,
- starzenie *modelu*: zrzut w szkle nie przyjmuje następnego checkpointu (§9).

Bez kalibracji po wypaleniu układ jest rzeźbą, nie akceleratorem. Kalibracja poprawia elektronikę i progi. Nie przepisuje \(n(\mathbf{r})\).

---

## 12. Argumenty za

1. **Wagi się nie ruszają.** To jest teza tej pracy. Przy dekodowaniu token po tokenie GPU czyta zestaw wag na każdy token, a arytmetyka jest przy tym tania (§3, §8.8). Wypalenie \(W\) w krysztale zdejmuje ten odczyt z magistrali. W cyfrowym krzemie ten sam ruch robi Taalas; cytujemy to jako kontekst, nie jako wynik OLLMU.
2. **Pasywne MAC są warunkiem z literatury, nie wynikiem.** Zgodnie z D²NN i z Nahmiasem szkło może być tańsze energetycznie na sam operator liniowy przy dużym \(N\) i precyzji rzędu kilku bitów. Warunek konieczny, żeby to przeżyło, stoi w §8.8: \(e_{\mathrm{conv}}\) i \(E_{\mathrm{src}}\) muszą zmieścić się pod progiem.
3. **Light cache** wykorzystuje to, że kryształ i tak opóźnia falę; wolniejszy ośrodek tam, gdzie krótkie echo jest użyteczne — nie jako KV-cache.
4. **Podział nośników.** Szkło trzyma to, co ma być duże i stałe. Krzem trzyma to, co zależy od wejścia albo co ma zostać wymienne: nieliniowość, router, uwagę, kontekst, małą poprawkę.
5. **Źródło BIC:** pokojowa temperatura, niski próg optyczny, wąska linia, silna polaryzacja — *jeśli* powstanie matryca elektryczna.
6. **Granica jest nazwana.** Faza 1 ma policzyć jedną warstwę, szum i energię na token, zanim ktokolwiek mówi o modelu.

## 13. Argumenty przeciw

1. Laser BIC 2026 nie jest matrycą elektryczną.
2. Krzem nie prowadzi 450 nm.
3. D²NN i fs-PNN są na MNIST / małych sieciach.
4. Każda nieliniowość i każdy router to światło–prąd–światło. Przetworniki i laser potrafią zjeść zysk z nieruchomych wag; §8.8 pokazuje próg, nie wygraną.
5. Interferencja w stałym rdzeniu nie jest wyborem top-k (§6.2).
6. Kerr na light cache ≠ ReLU.
7. Budżet ~4 bitów na macierz, błąd systematyczny zapisu i kaskada SOA bez 3R nie są pokazane jako znośne przez dziesiątki warstw (§8.7).
8. Gęstość z §8.9 to sufit. Modele frontier mają rząd \(10^{11}\)–\(10^{12}\) parametrów; użyteczna gęstość i mapa „waga = woksel” są otwarte.
9. Wypalony zrzut starzeje się w miesiącach i nie przyjmuje następnego checkpointu. GPU, które wagi wozi, przynajmniej potrafi je wymienić. Tu ta sama sztywność jest ostrzejsza (§9).
10. Bliźniak musi modelować krzem, przetwornik i Maxwella — drogi i przybliżony.
11. Brak publikacji, która składa te kawałki w LLM. Ta praca też tego nie robi.

---

## 14. Granica wersji 0.2

Wersja 0.1 stawiała routing interferencyjny jako sposób, w jaki geometria sama wybiera pamięć i ekspertów. Stały ośrodek liniowy tego nie umie; twierdzenie odpada (§6.2). Teza, która zostaje, jest węższa: zamrożony operator liniowy nie ściąga wag z pamięci przy każdym tokenie. Nieliniowość, uwaga i wybór eksperta zostają w elektronice, a ich koszt jest otwarty (§8.8). Żywotność zrzutu jest argumentem przeciw, nie za (§9, §13).

---

## 15. Fazy badawcze

Żadna faza nie jest wykonana w tym repozytorium. Repozytorium jest publiczną dokumentacją i nie zawiera symulatora.

Następny artefakt, który da się pokazać albo złożyć jako notę, to faza 1: mały różniczkowalny symulator, nie kryształ i nie model frontier.

| Faza | Cel | Kryterium sukcesu | Co nie jest sukcesem |
| --- | --- | --- | --- |
| 0 | Ten dokument | Etykiety Fakt/Hipoteza; teza o wagach na wierzchu; router opisany jako elektronika | „Mamy optyczny LLM” |
| 1 | Różniczkowalny symulator jednej warstwy FFN: \(W_1\), \(\varphi\), \(W_2\), szum analogowy, bilans \(E_{\mathrm{token}}\) | Kod; błąd MAC względem NumPy przy nazwanym SNR; \(E_{\mathrm{token}}\) przy nazwanych \(e_{\mathrm{bit}}\), \(e_{\mathrm{conv}}\), \(E_{\mathrm{src}}\) | Układ, skala GPT, werdykt że O-E-O wygrywa z GPU |
| 2 | Zabawkowa klasyfikacja (jak D²NN) | Trafność bliska cyfrowej przy kilku bitach SNR | Język naturalny |
| 3 | Ta sama warstwa przy szerokości 64–256 | Krzywa błędu od szerokości i od SNR | Transformer |
| 4 | Router elektroniczny i 2–4 optyczne ROM-y ekspertów | Top-k liczy krzem; port o stałym \(\mathbf{w}\) pokazany jako rzut, nie jako router | „Interferencyjny top-k” |
| 5 | Hybryda: uwaga cyfrowa + FFN optyczne | Energia *systemu* wobec odczytu wag na tym samym FFN | „Zero watów” |
| 6 | Źródło: od pompy optycznej do elektrycznej matrycy | Osobna linia materiałowa GaN | Wymagane do fazy 5 tylko jeśli SiN+InGaN nie wystarczy |
| 7 | Pasmo C + SOA między dwiema mini-cegłami | SNR analogowy po wzmacniaczu zmierzony, w tym kumulacja bez 3R | „Strata jak we włóknie 0,15 dB/km” |

---

## 16. Czego nie twierdzimy

- Że OLLMU istnieje w laboratorium.
- Że laser Fąsa et al. (2026) jest akceleratorem AI.
- Że energia inferencji LLM spadnie do ułamka promila GPU.
- Że bilans z §8.8 jest pomiarem albo wygraną nad GPU.
- Że pełny model frontier zmieści się w krysztale. §8.9 to sufit, nie pojemność.
- Że ~4 bity analogowe przeżywają dziesiątki warstw.
- Że interferencja w stałym rdzeniu wybiera ekspertów top-k albo włącza cache.
- Że Kerr na light cache jest ReLU.
- Że wolne światło zastąpi KV-cache.
- Że model bazowy uczy się *po* wypaleniu kryształu.
- Że zamrożenie wag jest zaletą, gdy zrzut żyje miesiące.
- Że błękitny laser pobije EUV w litografii.
- Że 0,15 dB/km włókna podmorskiego to strata wypalonego ROM-u.
- Że SOA/EDFA kopiują fotony bez szumu i bez zmiany fazy.
- Że licencja daje prawo wyłączne. To atrybucja. Publiczne ujawnienie przed zgłoszeniem odbiera nowość w systemie europejskim (art. 54 Konwencji o patencie europejskim). To informacja o ujawnieniu, nie opinia prawna.

---

## 17. Wnioski

OLLMU jest **nazwaną hipotezą o nośniku wag**. Przy dekodowaniu token po tokenie drogie jest wożenie wag, nie samo mnożenie. Wypalenie stałego \(W\) w szkle zdejmuje to wożenie z magistrali. W krzemie ten sam ruch jest już robiony na sztywno zapisanym modelu; szkło jest inną fizyką tego samego skrótu, nie skrótem darmowym.

Reszta jest warunkiem, pod którym teza przeżywa. Nieliniowość, uwaga i router zostają w elektronice, więc każda warstwa płaci światło–prąd–światło. Interferencja nie wybiera eksperta. Kilka bitów analogowych i sufit dyfrakcyjny nie są planem modelu o setkach miliardów parametrów. Zrzut w krysztale starzeje się razem z modelem i nie da się go dotrenować.

Najbardziej uczciwa następna praca to faza 1: różniczkowalny symulator jednej warstwy FFN, z szumem i z bilansem energii na token przy nazwanych założeniach. Dopiero taka nota ma co pokazać. Rzeźba modelu frontier nie ma.

---

## 18. Cytowania

Pełna lista z DOI (pozycje 1–21 sprawdzone w Crossref 2026-09-07; Horowitz i Taalas dopisane 2026-10-21): [REFERENCES.md](REFERENCES.md). Kluczowe: Fąs et al. 2026; Lin/Ozcan 2018; Shen 2017; Nahmias 2020; Horowitz 2014; Taalas (kontekst przemysłowy); Baba 2008; Vaswani 2017; Fedus 2022.
