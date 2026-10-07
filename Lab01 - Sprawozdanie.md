# Sprawozdanie

**Nr laboratorium:** [uzupełnić]  
**Temat:** Analiza wpływu reprezentacji zmiennoprzecinkowej na obliczanie ciągu  
**Autor:** [uzupełnić]  
**Data:** [uzupełnić]

---

## 1. Co było do zrobienia

Celem zadania było zbadanie wpływu sposobu reprezentacji liczb zmiennoprzecinkowych na wyniki obliczeń numerycznych.

W pierwszej części należało napisać program wyznaczający kolejne wyrazy ciągu określonego wzorem:

$$
x_{n+1}=x_n+3x_n(1-x_n),
$$

dla wartości początkowej:

$$
x_0=0.01.
$$

Obliczenia należało wykonać z wykorzystaniem dwóch typów danych: `float` oraz `double`, a następnie porównać otrzymane wyniki i wyjaśnić przyczynę ich rozbieżności.

W drugiej części należało obliczyć ten sam ciąg, wykorzystując równoważny algebraicznie wzór:

$$
x_{n+1}=4x_n-3x_n^2,
$$

a następnie porównać wyniki z rezultatami uzyskanymi przy użyciu pierwszego wzoru.

W ostatniej części należało wyznaczyć tzw. epsilon maszynowy, czyli najmniejszą wartość w reprezentacji zmiennoprzecinkowej, która pozwala odróżnić wynik $1+a$ od liczby $1$. Wartość tę wyznaczono osobno dla typów `float` i `double`.

---

## 2. Moje podejście do rozwiązania problemu

Do rozwiązania zadania wykorzystano język C. Zdecydowano się na ten język ze względu na możliwość bezpośredniego wykorzystania typów `float` i `double` oraz kontroli sposobu wykonywania operacji zmiennoprzecinkowych.

### 2.1. Obliczanie pierwszego wzoru

Dla pierwszej postaci ciągu zastosowano wzór:

$$
x_{n+1}=x_n+3x_n(1-x_n).
$$

W programie obliczenia wykonano równolegle dla dwóch typów danych:

```c
float xf = 0.01f;
double xd = 0.01;
```

Dla typu `float` wykorzystano również literały zakończone sufiksem `f`, np. `3.0f` oraz `1.0f`. Dzięki temu operacje przeznaczone dla `float` są wykonywane z wykorzystaniem wartości tego typu, zamiast domyślnych literałów typu `double`.

W każdej iteracji zapisywano aktualną wartość dla obu reprezentacji. Dodatkowo obliczano bezwzględną różnicę pomiędzy wynikami:

$$
d_n=\left|x_n^{float}-x_n^{double}\right|.
$$

Przy obliczaniu różnicy wartość `float` była jawnie konwertowana do `double`. Konwersja ta nie zwiększa dokładności wcześniej obliczonej wartości `float`; służy jedynie do umożliwienia wykonania odejmowania w typie `double`.

### 2.2. Obliczanie drugiego wzoru

Następnie zastosowano wzór:

$$
x_{n+1}=4x_n-3x_n^2.
$$

Wzór ten otrzymuje się przez algebraiczne przekształcenie pierwszej postaci:

$$
\begin{aligned}
x_n+3x_n(1-x_n)
&=x_n+3x_n-3x_n^2\\
&=4x_n-3x_n^2.
\end{aligned}
$$

Z matematycznego punktu widzenia oba wzory są więc równoważne.

W programie obliczenia ponownie wykonywano równolegle dla `float` i `double`. Pozwoliło to zbadać dwa różne efekty: wpływ dokładności reprezentacji oraz wpływ sposobu zapisania matematycznie tego samego wzoru.

### 2.3. Wyznaczenie epsilona maszynowego

Epsilon maszynowy wyznaczono eksperymentalnie. Rozpoczynano od wartości:

$$
a=1
$$

i w kolejnych krokach zmniejszano ją dwukrotnie:

$$
a\leftarrow\frac{a}{2}.
$$

Sprawdzano, czy nadal zachodzi:

$$
1+\frac{a}{2}>1.
$$

Proces kończył się, gdy dodanie połowy aktualnej wartości $a$ do liczby $1$ nie powodowało już zmiany reprezentowanej wartości:

$$
1+\frac{a}{2}=1.
$$

Otrzymana wartość $a$ odpowiada standardowo definiowanemu epsilonowi maszynowemu dla danej reprezentacji.

---

## 3. Wyniki

Obliczenia wykonano dla 50 iteracji. Poniższa tabela przedstawia wybrane wartości ciągu dla obu wzorów oraz obu typów danych.

| $n$ | Wzór 1 `float` | Wzór 1 `double` | Wzór 2 `float` | Wzór 2 `double` |
|---:|---:|---:|---:|---:|
| 0 | 0.010000000 | 0.010000000 | 0.010000000 | 0.010000000 |
| 1 | 0.039700002 | 0.039700000 | 0.039699998 | 0.039700000 |
| 2 | 0.154071733 | 0.154071730 | 0.154071719 | 0.154071730 |
| 5 | 0.171518803 | 0.171519142 | 0.171519280 | 0.171519142 |
| 10 | 0.722930610 | 0.722914301 | 0.722909331 | 0.722914301 |
| 15 | 1.270483732 | 1.270261774 | 1.270189524 | 1.270261774 |
| 20 | 0.579903603 | 0.596529313 | 0.601947010 | 0.596529313 |
| 25 | 1.007080555 | 1.315588346 | 1.333029985 | 1.315588346 |
| 30 | 0.752920926 | 0.374146490 | 0.287253678 | 0.374146508 |
| 40 | 0.258605480 | 0.011611238 | 0.985960722 | 0.011607295 |
| 50 | 1.064965844 | 1.313996747 | 1.199478865 | 1.308458032 |

Już od pierwszych iteracji można zaobserwować niewielkie różnice pomiędzy wartościami `float` i `double`. Różnice te początkowo są bardzo małe, jednak wraz ze wzrostem liczby iteracji stają się coraz większe.

Dla pierwszego wzoru bezwzględna różnica pomiędzy `float` i `double` wynosiła między innymi:

| $n$ | $\left|x_n^{float}-x_n^{double}\right|$ |
|---:|---:|
| 0 | $2.24\cdot10^{-10}$ |
| 5 | $3.39\cdot10^{-7}$ |
| 10 | $1.63\cdot10^{-5}$ |
| 15 | $2.22\cdot10^{-4}$ |
| 20 | $1.66\cdot10^{-2}$ |
| 24 | $2.53\cdot10^{-1}$ |
| 39 | $1.26$ |
| 50 | $2.49\cdot10^{-1}$ |

Oznacza to, że początkowo bardzo mały błąd reprezentacji może po kilkudziesięciu iteracjach doprowadzić do znacznej różnicy pomiędzy obliczeniami.

Podobne zachowanie zaobserwowano dla drugiego wzoru. Przykładowo, dla $n=29$ różnica pomiędzy wynikami `float` i `double` wyniosła około:

$$
1.16.
$$

### Epsilon maszynowy

Program wyznaczył następujące wartości:

| Typ | Epsilon maszynowy |
|---|---:|
| `float` | $1.1920929\cdot10^{-7}$ |
| `double` | $2.2204460492503131\cdot10^{-16}$ |

Wartości te różnią się o kilka rzędów wielkości. Oznacza to, że typ `double` pozwala reprezentować liczby z dużo większą dokładnością w otoczeniu liczby $1$ niż typ `float`.

---

## 4. Weryfikacja poprawności rozwiązania

Poprawność rozwiązania zweryfikowano na kilka sposobów.

Po pierwsze, oba wykorzystane wzory są matematycznie równoważne:

$$
x_n+3x_n(1-x_n)=4x_n-3x_n^2.
$$

W przypadku obliczeń wykonywanych z nieskończoną dokładnością powinny więc prowadzić do identycznych wyników. Otrzymanie różnych wartości w programie nie oznacza błędu we wzorze, lecz wynika z ograniczonej dokładności arytmetyki zmiennoprzecinkowej oraz z różnej kolejności wykonywania operacji.

Różnicę tę dobrze widać dla $n=24$. Dla pierwszego wzoru otrzymano:

$$
x_{24}^{float}=0.9964407086,
$$

natomiast dla drugiego:

$$
x_{24}^{float}=0.6566124558.
$$

Jednocześnie wyniki dla `double` wynosiły odpowiednio:

$$
x_{24}^{double}=0.7435756764
$$

oraz

$$
x_{24}^{double}=0.7435756767.
$$

Różnica pomiędzy wynikami `double` jest więc bardzo mała, podczas gdy dla `float` jest znaczna.

Po drugie, wyznaczony eksperymentalnie epsilon maszynowy można porównać ze standardowymi wartościami dostępnymi w bibliotece C poprzez `FLT_EPSILON` oraz `DBL_EPSILON`. Otrzymane wartości:

$$
1.1920929\cdot10^{-7}
$$

dla `float` oraz

$$
2.2204460492503131\cdot10^{-16}
$$

dla `double` odpowiadają standardowym wartościom dla typowych reprezentacji IEEE 754.

Po trzecie, zgodność początkowych wyników obu metod stanowi dodatkową kontrolę implementacji. Dla $x_0=0.01$ oba wzory dają teoretycznie:

$$
x_1=0.01+3\cdot0.01(1-0.01)=0.0397
$$

oraz:

$$
x_1=4\cdot0.01-3\cdot0.01^2=0.0397.
$$

Program daje wartości bardzo bliskie tej wartości dla obu typów danych, a dalsze rozbieżności pojawiają się stopniowo w wyniku kolejnych operacji zmiennoprzecinkowych.

---

## 5. Wnioski

Przeprowadzone doświadczenie pokazuje, że sposób reprezentacji liczb ma istotny wpływ na wyniki obliczeń numerycznych.

Najważniejszym zaobserwowanym zjawiskiem jest stopniowe narastanie różnic pomiędzy wynikami uzyskanymi przy użyciu `float` i `double`. Początkowe różnice są bardzo małe i wynikają z ograniczonej dokładności reprezentacji liczb. Następnie są one przenoszone do kolejnych iteracji, ponieważ wartość kolejnego wyrazu zależy bezpośrednio od poprzedniego. W efekcie niewielki błąd początkowy może zostać znacznie powiększony po wykonaniu wielu iteracji.

Istotne jest również to, że zastosowanie `double` nie powoduje całkowitego wyeliminowania błędów numerycznych. Zapewnia jednak znacznie większą dokładność niż `float`, dzięki czemu rozbieżności pojawiają się później i są początkowo znacznie mniejsze.

Drugim ważnym wnioskiem jest wpływ postaci wzoru na wynik obliczeń. Wzory

$$
x_{n+1}=x_n+3x_n(1-x_n)
$$

oraz

$$
x_{n+1}=4x_n-3x_n^2
$$

są równoważne matematycznie, ale nie są równoważne numerycznie. Wynika to z faktu, że komputer wykonuje skończoną liczbę operacji na liczbach o ograniczonej precyzji, a po poszczególnych operacjach występują zaokrąglenia. Zmiana kolejności operacji może więc prowadzić do innego wyniku.

Największe rozbieżności zaobserwowano dla typu `float`, którego epsilon maszynowy wynosi około $1.19\cdot10^{-7}$. Dla `double` epsilon wynosi około $2.22\cdot10^{-16}$, co pokazuje znacznie większą precyzję tej reprezentacji.

Eksperyment pokazuje zatem, że w obliczeniach numerycznych nie wystarczy znać jedynie matematycznego wzoru. Istotne są również sposób jego implementacji, zastosowany typ danych oraz wpływ błędów zaokrągleń propagujących się podczas kolejnych operacji.

---

## 6. Bibliografia

1. Dokumentacja języka C i biblioteki standardowej – opis typów zmiennoprzecinkowych oraz stałych `FLT_EPSILON` i `DBL_EPSILON`.
2. IEEE Computer Society, *IEEE Standard for Floating-Point Arithmetic (IEEE 754)*.
3. Materiały dydaktyczne do laboratorium – instrukcja do zadania dotyczącego arytmetyki zmiennoprzecinkowej.
