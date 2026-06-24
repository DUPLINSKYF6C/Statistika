# Statistika

# Štatistika — cheat sheet pre skúšku

---

## 1. Základné štatistiky (vždy ako prvé)

| Čo | Excel |
|---|---|
| Počet n | `=COUNT(A1:A35)` |
| Priemer x̄ | `=AVERAGE(A1:A35)` |
| Smerodajná odchýlka (výber) | `=STDEV.S(A1:A35)` |
| Smerodajná odchýlka (všetci) | `=STDEV.P(A1:A35)` |
| Rozptyl (výber) | `=VAR.S(A1:A35)` |
| Rozptyl (všetci) | `=VAR.P(A1:A35)` |

> **Pravidlo:** zadanie hovorí "náhodný výber" → `.S` | zadanie hovorí "všetci" → `.P`

---

## 2. Testovanie hypotéz — stredná hodnota

### Krok 1 — urči hypotézy zo zadania
| Zadanie hovorí | H₁ | H₀ | Typ testu |
|---|---|---|---|
| "väčšie ako" | μ > μ₀ | μ ≤ μ₀ | pravostranný |
| "menšie ako" | μ < μ₀ | μ ≥ μ₀ | ľavostranný |
| "líši sa / iná" | μ ≠ μ₀ | μ = μ₀ | obojstranný |

### Krok 2 — testovacia charakteristika
```excel
=(AVERAGE - μ₀) / (STDEV.S / SQRT(n))
```

### Krok 3 — kritický obor

| Typ testu | Rozptyl neznámy | Excel |
|---|---|---|
| Pravostranný | t > t₁₋α; n₋₁ | `=T.INV(1-α; n-1)` |
| Ľavostranný | t < −t₁₋α; n₋₁ | `=T.INV(α; n-1)` |
| Obojstranný | \|t\| > t₁₋α/₂; n₋₁ | `=T.INV(1-α/2; n-1)` |

> **Pravidlo:** rozptyl neznámy → T.INV | rozptyl známy → NORM.S.INV

### Krok 4 — rozhodnutie
- Leží t v kritickom obore? → **áno** → zamietame H₀ → minister **môže tvrdiť**
- Neleží? → **nie** → nezamietame H₀ → minister **nemôže tvrdiť**

---

## 3. Deskriptívna štatistika — surové dáta

| Čo | Excel |
|---|---|
| Medián (prostredná hodnota) | `=MEDIAN(A1:A48)` |
| Rozptyl | `=VAR.P(A1:A48)` |
| Q1 | `=QUARTILE.INC(A1:A48; 1)` |
| Q3 | `=QUARTILE.INC(A1:A48; 3)` |
| Kvartilové rozpätie RQ | `=Q3 - Q1` |
| Kvartilová odchýlka Q | `=(Q3 - Q1) / 2` |
| Relatívna kvartilová odchýlka VQ | `=Q / MEDIAN * 100` |
| Šikmosť γ₁ (výber) | `=SKEW(A1:A48)` |
| Šikmosť γ₁ (všetci) | `=SKEW.P(A1:A48)` |
| Špičatosť γ₂ | `=KURT(A1:A48)` |

### Záver pre šikmosť γ₁
| Hodnota | Záver |
|---|---|
| γ₁ < 0 | krivka je ľavostranná |
| γ₁ = 0 | krivka je symetrická |
| γ₁ > 0 | krivka je pravostranná |

### Záver pre špičatosť γ₂
| Hodnota | Záver |
|---|---|
| γ₂ < 0 | krivka je plochá |
| γ₂ = 0 | krivka je normálna (Gaussova) |
| γ₂ > 0 | krivka je špičatá |

---

## 4. Intervaly spoľahlivosti

### Pre priemer — rozptyl ZNÁMY (zadanie ti dá σ²)
```excel
dolná = priemer - NORM.S.INV(1-α/2) * (σ / SQRT(n))
horná = priemer + NORM.S.INV(1-α/2) * (σ / SQRT(n))
```
> σ = SQRT(zadaný rozptyl)

### Pre priemer — rozptyl NEZNÁMY
```excel
dolná = priemer - T.INV(1-α/2; n-1) * (s / SQRT(n))
horná = priemer + T.INV(1-α/2; n-1) * (s / SQRT(n))
```

### Pre podiel π
```excel
p = počet_spĺňajúcich / n          (napr. =COUNTIF(A1:A24;"<1000")/n)
dolná = p - NORM.S.INV(1-α/2) * SQRT(p*(1-p)/n)
horná = p + NORM.S.INV(1-α/2) * SQRT(p*(1-p)/n)
```

### Kvantil podľa α
| α | 1-α/2 | NORM.S.INV |
|---|---|---|
| 10% | 0,95 | 1,6449 |
| 5% | 0,975 | 1,9600 |
| 1% | 0,995 | 2,5758 |

---

## 5. Deskriptívna štatistika — intervalové rozdelenie

> Keď nemáš surové dáta ale tabuľku intervalov a počtov.
> Najprv vypočítaj stred každého intervalu: stred = (dolná + horná) / 2

| Čo | Excel |
|---|---|
| Priemer | `=SUMPRODUCT(stredy; pocty) / SUM(pocty)` |
| Smerodajná odchýlka | `=SQRT(SUMPRODUCT(pocty; (stredy-priemer)^2) / SUM(pocty))` |
| Šikmosť γ₁ | `=SUMPRODUCT(pocty; (stredy-priemer)^3) / (SUM(pocty) * s^3)` |
| Modus | stred intervalu s najväčším počtom |

### Kvartily pri intervalovom rozdelení
Vzorec z tabuľky vzorcov:
```
Q = a + (k/α · n − N_pred) / n_interval · h
```
- **a** = dolná hranica intervalu kde leží kvartil
- **k/α** = 0,25 pre Q1, 0,75 pre Q3
- **n** = celkový počet
- **N_pred** = kumulatívny počet pred intervalом
- **n_interval** = počet v intervale
- **h** = šírka intervalu

---

## Rýchly prehľad — ktorú funkciu kedy

| Situácia | Funkcia |
|---|---|
| Výber, σ neznáme | T.INV |
| Všetci alebo σ známe | NORM.S.INV |
| Výber → rozptyl | VAR.S |
| Všetci → rozptyl | VAR.P |
| Výber → šikmosť | SKEW |
| Všetci → šikmosť | SKEW.P |
| Spočítať podmienku | COUNTIF(rozsah; "<1000") |
