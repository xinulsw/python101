.. _widzety-qt5:

Widżety
###########################

.. highlight:: python

Prosta 1-okienkowa aplikacja prezentująca większość podstawowych widżetów dostępnych w bibliotece Qt6
za pomocą Pythona 3 i biblioteki **PyQt6**.
Przykład ilustruje również techniki `programowania obiektowego <https://pl.wikipedia.org/wiki/Programowanie_obiektowe>`_ (ang. *Object Oriented Programing*).

.. figure:: img/widzety.png

W wybranym katalogu przygotuj :ref:`środowisko wirtualne Pythona <venv>`.
Zainstaluj bibliotekę PyQt6 w aktywowanym środowisku:

.. code-block:: bash

    (.venv) pip install pyqt6

.. attention::

    **Wymagana wiedza**:

    * Znajomość Pythona w stopniu średnim.
    * Znajomość podstaw projektowania interfejsu z wykorzystaniem bibliotek Qt (zob. scenariusz :ref:`Kalkulator <kalkulator-qt>`).
    * Przedstawiona aplikacja składa się z 3 plików, które muszą być zapisane w tym samym katalogu,
      np. :file:`widzety`.

QPainter – podstawy rysowania
*****************************

Zaczynamy od utworzenia głównego pliku aplikacji o nazwie :file:`widzety.py`.
Wstawiamy do niego poniższy kod:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z1.py
    :linenos:

Klasa ``Widgety`` posłuży do zdefiniowania głównego okna oraz logiki działania naszej aplikacji.
Dziedziczy z klasy ``QWidget`` – podstawowej klasy biblioteki Qt, która jest bazą dla każdego elementu GUI,
w tym okna głównego. Dziedziczy również z klasy ``UiWidget`` importowanej z pliku :file:`gui.py`,
w którym zdefiniujemy elementy interfejsu graficznego.

W konstruktorze klasy (``__init()__``) wywołujemy konstruktory klas rodziców (``super().__init__()``)
oraz ustawiamy tytuł okna aplikacji (``self.setWindowTitle('Widżety')``).

Pozostały kod tworzy instancję aplikacji w oparciu o klasę ``QApplication``, a także
instancję okna głównego, czyli klasy ``Widgety``, wyświetla je i uruchamia pętlę zdarzeń.

Kod klasy ``UiWidget`` umieszczamy we wspomnianym pliku o nazwie :file:`gui.py`:

.. raw:: html

    <div class="code_no">Plik <i>gui.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: gui_z1.py
    :linenos:

W konstruktorze klasy tworzymy widżet ``ksztalt1``, który będzie mógł rysować figury geometryczne.
Widżet jest instancję klasy ``Ksztalt`` zaimportowanej z pliku :file:`ksztalty.py`.
Z tego pliku importujemy również klasę ``Ksztalty``, której właściwości oznaczają rysowane figury,
w tym przypadku prostokąt: ``self.ksztalt1 = Ksztalt(None, Ksztalty.Rect)``

Jeden widżet może zawierać wiele różnych elementów GUI, które trzeba w jakiś sposób porozmieszczać.
Służą do tego układy graficzne (ang. *layouts*). Biblioteka Qt udostępnia następujące układy
poziomy (`QHBoxLayout <https://doc.qt.io/qt-6/qhboxlayout.html>`_),
pionowy (`QVBoxLayout <https://doc.qt.io/qt-6/qvboxlayout.html>`_)
i tabelaryczny (`QGridLayout <https://doc.qt.io/qt-6/qgridlayout.html>`_).

Rysowany kształt dodajemy do układu poziomego za pomocą metody ``addWidget()``.
Następnie sam układ poziomy dodajemy do pionowego układu okna za pomocą metody ``addLayout()``.
Główny układ okna naszego widżetu ustawiamy w metodzie ``setLayout()``.

Klasa *Ksztalt*
***************

W pliku :file:`ksztalty.py` umieszczamy poniższy kod:

.. raw:: html

    <div class="code_no">Plik <i>ksztalty.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: ksztalty.py
    :linenos:
    :lines: 1-50

Za pomocą klasy ``Ksztalty`` symulujemy typ wyliczeniowy, tzn. angielskim nazwom kształtów,
które będą dostępne jako dane statyczne klasy, przypisujemy kolejne liczby całkowite zaczynając od 0.
Kształty, które będziemy rysowali, to:

 * *RECT* – prostokąt, wartość 0;
 * *ELLIPSE* – elipsa, w tym koło, wartość 1;
 * *POLYGON* – linia łamana zamknięta, np. trójkąt, wartość 2;
 * *LINE* – linia łącząca dwa punkty, wartość 3.

Klasa ``Ksztalt`` dziedziczy z klasy ``QWidget`` i pozwoli na rysowanie zdefiniowanych
w klasie ``Ksztalty`` figur. W konstruktorze definiujemy właściwości obiektu, który ma być rysowany:

- ``self.ksztalt`` – rysowana figura wskazana w parametrze ``ksztalt``, której domyślna wartość to
  ``Ksztalty.RECT``,
- ``self.kolor_o``, ``self.kolor_w`` – kolory obramowania i wypełnienia.

Kolory tworzymy za pomocą klasy `QColor <https://doc.qt.io/qt-6/qcolor.html>`_,
używając formatu `RGB <https://pl.wikipedia.org/wiki/RGB>`_, np .: ``QColor(0, 0, 0)``.

Za rysowanie każdego widżetu odpowiada metoda `paintEvent() <https://doc.qt.io/qt-6/qwidget.html#paintEvent>`_.
Nadpisujemy ją. Tworzymy instancję klasy `QPainter <https://doc.qt.io/qt-6/qpainter.html>`_
umożliwiającej rysowanie różnych kształtów (``qp = QPainter()``). Między metodami ``begin()`` i ``end()``
wywołujemy metodę ``rysuj_figury()``, w której implementujemy kod rysujący poszczególne kształty.

Metoda ``rysuj_figury()`` otrzymuje obiekt klasy ``QPainter``. Jego metody ``setPen()`` i ``setBrush()``
pozwalają ustawić kolor odpowiednio obramowania i wypełnienia. Następnie w instrukcji warunkowej
sprawdzamy rodzaj rysowanego kształtu i wywołujemy metodę rysującą odpowiednią figurę:

* ``drawRect()`` – rysuje prostokąt,
* ``drawEllipse()`` – rysuje elipsę (koło),
* ``qp.drawLine()`` – pozwala narysować linię wyznaczoną przez współrzędne punktu
  początkowego i końcowego typu ``QPoint``; nasza klasa wykorzystuje tu współrzędne
  lewego górnego (``self.prost.topLeft()``) i prawego dolnego (``self.prost.bottomRight()``)
  rogu domyślnego prostokąta ``prost``,
* ``drawPolygon()`` – pozwala rysować wielokąty, jako argument podajemy listę typu
  `QPolygon <https://doc.qt.io/qt-6/qpolygon.html>`_ punktów typu `QPoint <https://doc.qt.io/qt-6/qpoint.html>`_
  opisujących współrzędne kolejnych wierzchołków; domyślne współrzędne zdefiniowane zostały
  jako atrybut ``punkty`` klasy ``Ksztalty``,

.. note::

    Każdy rysowany kształt wpisany jest w prostokąt zdefiniowany jako właściwość statyczna
    klasy ``Ksztalt``: ``prost = QRect(1, 1, 101, 101)``.
    Obiekt ten jest instancją klasy `QRect <https://doc.qt.io/qt-6/qrect.html>`_.
    Dwie pierwsze wartości to współrzędne lewego górnego, a dwie następne prawego dolnego rogu prostokąta
    w 2-wymiarowym układzie współrzędnych.

    Początek układu współrzędnych, w odniesieniu do którego definiujemy w Qt pozycję widżetów
    czy punkty opisujące kształty, znajduje się w lewym górnym rogu obiektu rodzica,
    np. głównego okna aplikacji.

.. note::

    Warto zrozumieć różnicę pomiędzy **zmiennymi klasy** a **zmiennymi instancji**.
    Zmienne (właściwości, atrybuty) klasy, określane również jako dane statyczne, są wspólne
    dla wszystkich jej instancji. W naszej aplikacji zdefiniowaliśmy w ten sposób
    zmienne ``prost`` i ``punkty`` klasy ``Ksztalt``.

    Zmienne instancji natomiast są inne dla każdego obiektu.
    Definiujemy je w konstruktorze, używając słowa ``self``. Np. każda instancja klasy
    ``Ksztalt`` może mieć inną wartość właściwości ``self.ksztalt``.

    Zob.: `Class and Instance Variables <https://docs.python.org/3/tutorial/classes.html#class-and-instance-variables>`_

**Ćwiczenie**

    * Uruchom skrypt :file:`widzety.py`.
    * Spróbuj zmienić rodzaj rysowanej figury oraz kolory jej obramowania i wypełnienia.

.. figure:: img/widzety00.png

Być może zauważysz, że po uruchomieniu naszego skryptu rozmiar okna aplikacji nie jest dopasowany
do rozmiaru rysowanej figury. Spróbujemy to zmienić uzupełniając kod klasy ``Ksztalt``:

.. raw:: html

    <div class="code_no">Plik <i>ksztalty.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: ksztalty.py
    :linenos:
    :lineno-start: 51
    :lines: 51-

W nadpisanych metodach ``sizeHint()`` i ``minimumSizeHint()`` określamy sugerowany i minimalny
rozmiar naszego kształtu. Są one niezbędne, aby układy graficzne (ang. *layouts*), w których
umieścimy kształty, zarezerwowały odpowiednio dużo miejsca na ich wyświetlenie.

Kod uzupełniliśmy również o dwie metody ``ustaw_ksztalt()`` i ``ustaw_kolor_w()``, które przydadzą się nam dalej.
Jak wskazują nazwy – pozwolą one zmieniać kształt i jego kolor wypełnienia już po utworzeniu obiektu.
Metoda ``self.update()`` wymusi ponowne narysowanie kształtu.

**Ćwiczenie**

    * Ponownie przetestuj działanie aplikacji, spróbuj zmienić rodzaj rysowanej figury oraz
      kolor jej wypełnienia.

.. figure:: img/widzety01.png

.. note::

    W kolejnych krokach będziemy dodawać widżety różnego typu. Kod tworzący odpowiednie obiekty
    i ustawiający ich początkowe właściwości dopisywać będziemy w pliku :file:`gui.py`
    w konstruktorze klasy ``UiWidget``. Dodając widżety, musimy pamiętać o zaimportowaniu
    odpowiedniej klasy z ``PyQt6.QtWidgets`` na początku pliku.

    Kod wiążący sygnały ze slotami umieścimy w pliku :file:`widzety.py`,
    w konstruktorze klasy ``Widgety``. Sloty implementować będziemy jako funkcje
    tej klasy.

Przyciski CheckBox
******************

Wykorzystując klasę ``Ksztalt`` utworzymy kolejny obiekt do rysowania figur. Dodamy także
przyciski typu `QCheckBox <https://doc.qt.io/qt-6/qcheckbox.html>`_ umożliwiające zmianę
rodzaju wyświetlanej figury.

**Importy** w pliku :file:`gui.py`:

.. code-block:: python

    from PyQt6.QtWidgets import QCheckBox, QButtonGroup

Klasa ``UiWidget`` przyjmuje następującą postać:

.. raw:: html

    <div class="code_no">Plik <i>gui.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: gui_z2.py
    :linenos:
    :lineno-start: 6
    :lines: 6-

Dodajemy drugi obiekt ``self.ksztalt2``, domyślnie rysujący elipsę.
DEfiniujemy też dodatkową właściwość ``self.ksztalt_aktywny``, która przechowywała będzie
aktualnie wybrany kształt, tzn. albo ``ksztal1`` (domyślnie) albo ``ksztalt2``.

Do tworzenia przycisków typu CheckBox wykorzystujemy pętlę ``for``, która odczytuje z krotki
kolejne indeksy i etykiety przycisków. Jeśli masz wątpliwości, jak to działa,
przetestuj następujący kod w konsoli Pythona:

.. code-block:: bash

    >>> for i, v in enumerate(('Kwadrat', 'Koło', 'Trójkąt', 'Linia')):
    ...   print(i, v)

Odczytane etykiety przekazujemy do konstruktora: ``self.chk = QCheckBox(v)``.

Przyciski wyboru kształtu działać mają na zasadzie wyłączności, w danym momencie
powinien być zaznaczony tylko jeden z nich. Tworzymy więc grupę logiczną ``grupa_chk`` na podstawie
klasy `QButtonGroup <https://doc.qt.io/qt-6/qbuttongroup.html>`_.
Do grupy dodajemy przyciski, oznaczając je kolejnymi indeksami:
``self.grupa_chk.addButton(self.chk, i)``.

Metoda ``buttons()`` zwraca listę przycisków, którą zapisujemy w zmiennej ``przyciski``.
Przycisk odpowiadający aktualnemu kształtowi wskazujemy przez indeks ``self.ksztalt_aktywny.ksztalt``
i wywołujemy metodę ``setChecked(True)``, która go zaznacza.

Poza pętlą tworzymy jeszcze jeden przycisk (``self.ksztaltChk = QCheckBox("<=")``),
niezależny od powyższej grupy. Jego stan wskazuje aktywny kształt.
Domyślnie go zaznaczamy: ``self.ksztaltChk.setChecked(True)``, co oznacza,
że aktywną figurą będzie pierwszy kształt.

Wszystkie elementy interfejsu umieszczamy w układzie poziomym o nazwie ``uklad_h1``.
Po lewej stronie znajdzie się ``ksztalt1``, w środku układ przycisków wyboru,
a po prawej ``ksztalt2``.

Obsługa sygnałów
================

Teraz zajmiemy się obsługą sygnałów. Przypomnijmy, że są to wydarzenia zachodzące w obrębie okna
naszej aplikacji (ruch myszy, kliknięcia, naciśnięcia klawiszy itp.) przechwytywane przez
główną pętlę zdarzeń naszej aplikacji. Do ich obsługi używamy slotów, czyli funkcji,
w tym wypadku będą to metody klasy ``Widgety``.

W pliku :file:`widzety.py` rozbudowujemy klasę ``Widgety``:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z2.py
    :linenos:
    :lineno-start: 5
    :lines: 5-31

Grupa przycisków ``grupa_chk`` po kliknięciu emituje sygnał ``buttonClicked()```.
Przekazujemy jego obsługę do slotu (metody klasy ``Widgety``) ``ustaw_ksztalt()``.

W slocie ``ustaw_ksztalt()`` używamy metody o tej samej nazwie klasy ``Ksztalt``
do ustawienia nowej figury do narysowania. Jako argument przekazujemy
identyfikator klikniętego przycisku odczytywany za pomocą metody ``checkedId()``.
Jest to liczba całkowita, która wskazuje jedną z figur zdefiniowanych w klasie ``Ksztalty``.

Przypomnijmy (zob. wyżej), że metoda ``ustaw_ksztalt()`` z klasy ``Kształt`` aktualizuje
identyfikator figury i wywołuje metodę ``update()``, która wywołuje metodę
``paintEvent()``, a ta metodę ``rysuj_figury()``, która rysuje nową figurę.

Kliknięcie przycisku checkbox wskazującego aktywną figurę obsługujemy za pomocą
slotu ``aktywuj_ksztalt()``. Jej zadaniem jest ustawienie pierwszego lub drugiego
kształtu jako aktywnego. Jeżeli przekazany do slotu argument ``wartosc`` będzie
miał wartość ``True``, co oznacza, że checkbox został zaznaczony, aktywujemy
``ksztalt1``, w przeciwnym razie ``ksztalt2``. Zmieniamy również odpowiednio
tekst wyświetlany przy przycisku.

.. note::

    Warto zapamiętać, jak uzyskać dostęp do obiektu nadawcy, który wygenerował dany sygnał.
    W odpowiednim slocie używamy kodu ``self.sender()``.

**Ćwiczenie**

    Uruchom kilkakrotnie aplikację. Spróbuj zmieniać inicjalne rodzaje domyślnych
    kształtów i kolory wypełnienia figur.

.. figure:: img/widzety02.png

Slider i przyciski RadioButton
******************************

Możemy już manipulować rodzajami rysowanych kształtów na obydwu obszarach rysowania.
Spróbujemy teraz dodać widżety pozwalające je kolorować.

W pliku :file:`gui.py` dodajemy importy:

.. code-block:: python

    from PyQt6.QtCore import Qt
    from PyQt6.QtWidgets import QSlider, QLCDNumber, QSplitter
    from PyQt6.QtWidgets import QRadioButton, QGroupBox

Teraz rozbudowujemy konstruktor klasy ``UiWidget``. Po komentarzu ``# koniec CheckBox``
wstawiamy:

.. raw:: html

    <div class="code_no">Plik <i>gui.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: gui_z3.py
    :linenos:
    :lineno-start: 41
    :lines: 41-71

Do zmiany wartości składowych kolorów RGB wykorzystamy instancję klasy `QSlider <https://doc.qt.io/qt-6/qslider.html>`_,
czyli popularny suwak, w tym wypadku poziomy. Po utworzeniu obiektu, ustawiamy za pomocą
metod ``setMinimum()`` i ``setMaximum()`` zakres zmienianych wartości ``<0-255>``.

Następnie tworzymy instancję klasy `QLCDNumber <https://doc.qt.io/qt-6/qlcdnumber.html>`_,
którą wykorzystamy do wyświetlania wartości wybranej za pomocą suwaka.
Obydwa obiekty dodajemy do poziomego układu, rozdzielając je instancją typu
`QSplitter <https://doc.qt.io/qt-6/qsplitter.html>`_. Obiekt tez pozwala płynnie
zmieniać rozmiar otaczających go widżetów.

Przyciski typu `RadioButton <https://doc.qt.io/qt-6/qradiobutton.html>`_ posłużą nam do wskazywania
kanału koloru RGB, którego wartość chcemy zmienić. Tworzymy je w pętli,
wykorzystując odczytane z tupli nazwy kanałów: ``self.radio = QRadioButton(v)``.
Przyciski rozmieszczamy w układzie poziomym (``self.uklad_r.addWidget(self.radio)``).

Pierwszy z nich zaznaczamy: ``self.uklad_r.itemAt(0).widget().setChecked(True)``.
Metoda ``itemAt(0)`` zwraca nam pierwszy element danego układu jako typ ``QLayoutItem``.
Kolejna metoda ``widget()`` przekształca go w obiekt typu ``QWidget``,
dzięki czemu możemy wywoływać jego metody.

Układ przycisków dodajemy do grupy typu `QGroupBox <https://doc.qt.io/qt-6/qgroupbox.html>`_:
``self.grupa_rb.setLayout(self.uklad_r)``. Tego typu grupa zapewnia graficzną
ramkę z przyciskiem aktywującym typu CheckBox, który domyślnie zaznaczamy:
``self.grupa_rb.setCheckable(True)``. Za pomocą metody ``setObjectName()``
grupie nadajemy nazwę *Radio*. Grupę dodajemy do układu poziomego.

Wszystkie dodane powyżej widżety zostały umieszczone w układach poziomych,
które należy dodać do głównego układu okna. Dopisz przed wywołaniem metody ``setLayout()``
odpowiedni kod:

.. raw:: html

    <div class="code_no">Plik <i>gui.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. code-block:: python

        uklad_okna.addWidget(uklad_h2)
        uklad_okna.addLayout(uklad_h3)

Obsługa sygnałów
================

W pliku :file:`widzety.py` dodajemy importy:

.. code-block:: python

    from PyQt6.QtGui import QColor
    from PyQt6.QtWidgets import QRadioButton

Uzupełniamy konstruktor (``__init__()``) klasy ``Widgety``:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z3.py
    :linenos:
    :lineno-start: 18
    :lines: 18-25

Zmiana stanu przycisku *RadioButton* emituje sygnał ``toggled``. W pętli
``for i in range(self.uklad_r.count()):`` wiążemy ten sygnał dla każdego
przycisku układu ze slotem ``ustaw_kanal()``:
``self.uklad_r.itemAt(i).widget().toggled.connect(self.ustaw_kanal)``.

Przesuwanie suwaka wyzwala sygnał ``valueChanged``, który łączymy ze slotem
``zmien_kolor()``: ``self.suwak.valueChanged.connect(self.zmienKolor)``.

Do klasy ``Widget`` dodajemy teraz wspomniane sloty i metodę pomocniczą:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z3.py
    :linenos:
    :lineno-start: 41
    :lines: 41-70

Metoda ``ustaw_kanal()`` służy do zapisania w zbiorze kanałów ``self.kanaly`` litery
oznaczającej wybrany kanał. Kanał można wybrać za pomocą różnych widżetów, dlatego na początku
w zmiennej ``nadawca`` zapisujemy obiekt nadawcy. Warunek ``isinstance(nadawca, QRadioButton) and wartosc``
sprawdza za poocą funkcji wbudowanej ``isinstance(nadawca, QRadioButton)``, czy nadawcą jest przycisk *RadioButton*
i jeżeli tak, czy parametr ``wartosc`` ma wartość ``True``, co oznacza, że przycisk jest zaznaczony.
Jeżeli zostanie spełniony, do zresetowanego wcześniej zbioru kanałów dodajemy literę wybranego kanału:
``self.kanaly.add(nadawca.text())``. Następnie wywołujemy metodę ``wypisz_kanal()``.

Zadaniem metody ``wypisz_kanal()`` jest ustawienie wartości kanału przekazanego w parametrze ``kanal``
w widżecie przekazanym w parametrze ``widzet`` za pomocą metody ``setValue()``. Przekazny kanał wykkrywamy
w złożonej instrukcji warunkowej, składową koloru odczytujemy za pomocą odpowiednich metod, np.:
``self.suwak.setValue(self.kolor_w.red())``.

Metoda ``zmien_kolor()`` wywoływana jest po zmianie wartości, tj. liczby z zakresu ``<0; 255>``,
za pomocą suwaka. Wartość wyświetlamy w widżecie LCD: ``self.lcd.display(wartosc)``.
Następnie sprawdzamy, który ze zmienianych kanałów znajduje się w zbiorze kanały i aktualizujemy
jego wartość w kolorze wypełnienia, np.: ``self.kolor_w.setRed(wartosc)``.

Na koniec składowe koloru wypełnienia ``kolor_w`` przekazujemy do metody
``ustaw_kolor_w()`` aktywnego kształtu. Przypomnijmy, żę metoda ta zdefiniowana w pliku :file:`ksztalty.py`
aktualizuje kolor kształtu i wymusza jego ponowne rysowanie.

Przetestuj działanie aplikacji.

.. figure:: img/widzety03.png

ComboBox i SpinBox
******************

Modyfikowane kanały koloru można również wybierać z rozwijalnej listy typu
`QComboBox <https://doc.qt.io/qt-6/qcombobox.html>`_, a ich wartości
ustawiać za pomocą widżetu `QSpinBox <https://doc.qt.io/qt-6/qspinbox.html>`_.

W pliku :file:`gui.py` dodajemy importy:

.. code-block:: python

    from PyQt6.QtWidgets import QComboBox, QSpinBox

Po komentarzu ``# koniec RadioButton`` uzupełniamy konstruktor klasy ``UiWidget``:

.. raw:: html

    <div class="code_no">Plik <i>gui.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: gui_z4.py
    :linenos:
    :lineno-start: 73
    :lines: 73-91

Do listy utworzonej na podstawie klasy ``ComboBox`` dodajemy za pomocą pętli ``for``
litery poszczególnych kanałów: ``self.lista_rgb.addItem(v)``.

Obiekt typu *SpinBox* podobnie jak *Slider* wymaga ustawienia zakresu wartości ``<0-255>``.
Stosujemy takie same metody, jak wcześniej, tj. ``setMinimum()`` i ``setMaximum()``.

Obydwa widżety na początku wyłączamy metodą ``setEnabled(False)``. Umieszczamy jeden nad drugim
w pionowym układzie ``uklad_v1``, a układ dodajemy obok przycisków Radio ``uklad_h3.addLayout(uklad_v1)``,
oddzielając go odstępem 25 px: ``uklad_h3.insertSpacing(1, 25)``.

Obsługa sygnałóW
=================

W pliku :file:`widzety.py` dodajemy import:

.. code-block:: python

    from PyQt6.QtWidgets import QRadioButton

Do konstruktora dodajemy kod przechwytujący 3 sygnały:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z4.py
    :linenos:
    :lineno-start: 27
    :lines: 27-31

Następnie do klasy ``Widgety`` dodajemy slot ``ustaw_stan()``, który obsłuży kliknięcie
przycisku *CheckBox* z tekstem *Opcje RGB* umożliwiającego wybór spsosobu ustawiania składowych
koloru wypełnienia:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z4.py
    :linenos:
    :lineno-start: 80
    :lines: 80-93

Kliknięcie wspomnianego przycisku przechwytujemy: ``self.grupa_rb.clicked.connect(self.ustaw_stan)``.
Jeżeli funkcja ``ustaw_stan()`` w parametrze ``wartosc`` otrzyma ``True``, tzn. przycisk 
jest zaznaczony, wyłączamy widżety *ComboBox* i *SpinBox* (``setEnabled(False)``).
W przeciwnym razie je włączamy (``setEnabled(True)``), a także resetujemy zbiór kanałów
i dodajemy do niego kanał wybrany na liście: ``self.kanaly.add(self.lista_rgb.currentText())``.
Na koniec ustawiamy wartość aktywnego kanału w obiekcie *SpinBox*.

Zmianę kanału na liście *ComboBox*, tj. sygnał ``currentTextChanged`` obsługujemy za pomocą dodanej
wcześniej metody ``ustaw_kanal()``, która przyjmuje następującą postać:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z4.py
    :linenos:
    :lineno-start: 46
    :lines: 46-58

Dodajemy warunek ``isinstance(nadawca, QComboBox)`` sprawdzający, czy nadawcą jest obiekt typu ``QComboBox``.
Jeżeli tak, resetujemy zbiór kanałów i dodajemy literę wybranego kanału: ``self.kanaly.add(wartosc)``.
Na koniec ustawiamy wartość tego kanału w obiekcie *SpinBox*: ``self.wypisz_kanal(wartosc, self.spin_rgb)``.

.. note::

    Slot ``ustaw_kanal()`` w przypadku sygnału ``toogled`` obiektu typu ``QRadioButton`` otrzymuje
    w parametrze ``wartosc`` wartość ``True`` lub ``False`` w zależności od tego, czy przycisk jest zaznaczony
    czy nie. W przypadku sygnału ``currentTextChanged`` obiektu typu ``QComboBox``
    parametr ``wartosc`` zawiera literę wybranego kanału.

Zmiana wartości w kontrolce *SpinBox*, czyli sygnał ``valueChanged``, przekierowujemy
do dodanego wcześniej slotu ``zmien_lolor()``, który obsługuje również zmiany wartości na suwaku.

Uruchom aplikację i sprawdź jej działanie.

.. figure:: img/widzety04.png

Przyciski PushButton
********************

Do tej pory można było zmieniać kolor każdego kanału składowego osobno.
Dodamy teraz grupę przycisków typu `QPushButton <https://doc.qt.io/qt-6/qpushbutton.html>`_,
które zachowywać się będą jak grupa przycisków wielokrotnego wyboru.

**Importy** w pliku :file:`gui.py`:

.. code-block:: python

    from PyQt6.QtWidgets import QPushButton

Następnie po komentarzu ``# koniec ComboBox i SpinBox ###`` dopisujemy kod w funkcji ``setupUi()``:

.. raw:: html

    <div class="code_no">Plik <i>gui.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: gui_z5.py
    :linenos:
    :lineno-start: 92
    :lines: 92-107
    :emphasize-lines: 4, 6-8

Przyciski, jak poprzednio, tworzymy w pętli, podając w konstruktorze litery
składowych koloru RGB: ``self.btn = QPushButton(v)``. Każdy przycisk przekształcamy
na stanowy (może być trwale wciśnięty) za pomocą metody ``setCheckable()``.
Kolejne obiekty dodajemy do grupy logicznej typu `QButtonGroup <https://doc.qt.io/qt-6/qbuttongroup.html>`_:
``self.grupaP.addButton(self.btn)``; oraz do układu poziomego.
Układ przycisków dodajemy do ramki typu `QGropBox <https://doc.qt.io/qt-6/qgroupbox.html>`_ z przyciskiem CheckBox:
``self.grupaPBtn.setCheckable(True)``. Na początku ramkę wyłączamy: ``self.grupaPBtn.setChecked(False)``.

**Uwaga**: na koniec musimy dodać grupę przycisków do głównego układu okna:
``ukladOkna.addWidget(self.grupaPBtn)``. Inaczej nie zobaczymy jej w oknie aplikacji!

W pliku :file:`widzety.py` jak zwykle dopisujemy obsługę sygnałów w konstruktorze
i jedną nową funkcję:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z5.py
    :linenos:
    :lineno-start: 32
    :lines: 32-42

Pętla ``for btn in self.grupaP.buttons():`` odczytuje kolejne przyciski
z grupy ``grupaP``, i kliknięcie każdego wiąże z nową funkcją:
``btn.clicked[bool].connect(self.ustawKanalPBtn)``. Zadaniem funkcji
jest dodawanie kanału do zbioru, jeżeli przycisk został wciśnięty,
i usuwanie ich ze zbioru w przeciwnym razie. Inaczej niż w poprzednich
funkcjach, obsługujących przyciski *Radio* i listę *ComboBox*, nie resetujemy
tu zbioru kanałów.

Przetestuj zmodyfikowaną aplikację.

.. figure:: img/widzety05.png

QLabel i QLineEdit
******************

Dodamy do aplikacji zestaw widżetów wyświetlających aktywne kanały jako etykiety
typu `QLabel <https://doc.qt.io/qt-6/qlabel.html>`_ oraz wartości składowych koloru
jako 1-liniowe pola edycyjne typu `QLineEdit <https://doc.qt.io/qt-6/qlineedit.html>`_.

**Importy** w pliku :file:`gui.py`:

.. code-block:: python

    from PyQt6.QtWidgets import QLabel, QLineEdit

Następnie po komentarzu ``# koniec PushButton ###`` uzupełnij funkcję ``setupUi()``:

.. raw:: html

    <div class="code_no">Plik <i>gui.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: gui_z6.py
    :linenos:
    :lineno-start: 110
    :lines: 110-124
    :emphasize-lines: 10-11

Zaczynamy od utworzenia trzech etykiet i trzech pól edycyjnych dla każdego kanału.
W pętli wykorzystujemy funkcję Pythona
`getattr(obiekt, nazwa) <https://docs.python.org/3/library/functions.html#getattr>`_,
która potrafi zwrócić podany jako ``nazwa`` atrybut ``obiektu``. W tym wypadku
kolejne etykiety i pola edycyjne, które umieszczamy obok siebie w poziomie.
Przy okazji ograniczamy długość wpisywanego w pola edycyjne tekstu do 3 znaków:
``kolor.setMaxLength(3)``.

**Uwaga**: Pamiętajmy, że aby zobaczyć utworzone obiekty w oknie aplikacji, musimy dołączyć
je do głównego układu okna: ``ukladOkna.addLayout(ukladH4)``.

W pliku :file:`widzety.py` rozszerzamy konstruktor klasy ``Widgety`` i dodajemy
funkcję informacyjną:

.. raw:: html

    <div class="code_no">Plik <i>widzety.py</i>. Kod nr <script>var code_no = code_no || 1; document.write(code_no++);</script></div>

.. highlight:: python
.. literalinclude:: widzety_z6.py
    :linenos:
    :lineno-start: 36
    :lines: 36-57

W pętli, podobnej jak w pliku interfejsu, sygnał zmiany tekstu pola typu *QLineEdit*
wiążemy z dodaną wcześniej funkcją ``zmienKolor()``. Będziemy mogli wpisywać w tych
polach nowe wartości składowych koloru. **Ale uwaga**: do tej pory funkcja ``zmienKolor()``
otrzymywała wartości typu całkowitego z suwaka *QSlider* lub pola *QSpinBox*. Pole edycyjne
zwraca natomiast tekst, który trzeba rzutować na typ całkowity.
Dodaj więc na początku funkcji instrukcję: ``wartosc = int(wartosc)``.

Druga nowa rzecz to funkcja informacyjna ``info()``. Jej zadanie polega na wyróżnieniu
aktywnych kanałów poprzez pogrubienie czcionki etykiet i uaktywnieniu odpowiednich pól edycyjnych.
Jeżeli kanał jest nieaktywny, ustawiamy normalną czcionkę etykiety i wyłączamy pole edycji.
Wszystko dzieje się w pętli wykorzystującej omawiane już funkcje ``getattr()`` oraz ``setEnabled()``.

Na uwagę zasługują operacje na czcionce. Zmieniamy ją dzięki stylom CSS zdefiniowanym na
początku funkcji pod nazwą ``fontB`` i ``fontN``. Później przypisujemy je etykietom
za pomocą metody ``setStyleSheet()``.

Na końcu omawianej funkcji do każdego pola edycyjnego wstawiamy aktualną wartość
odpowiedniej składowej koloru przekształconą na tekst,
np. ``self.kolorR.setText(str(self.kolorW.red()))``.

Wywołanie tej funkcji w postaci ``self.info()`` powinniśmy dopisać przynajmniej
do funkcji ``zmienKolor()``.

Wprowadź omówione zmiany i przetestuj działanie aplikacji.

.. figure:: img/widzety06.png

Dodatki
********

Nasza aplikacja działa, ale można dopracować w niej kilka szczegółów. Poniżej zaproponujemy
kilka zmian, które potraktować należy jako zachętę do samodzielnych ćwiczeń i przeróbek.

1. Po pierwsze pola edycyjne *QLineEdit* dla składowych zielonej i niebieskiej powinny
   być na początku nieaktywne. Dodaj odpowiedni kod do pliku :file:`gui.py`,
   wykorzystaj metodę ``setEnabled()``.
2. Zaznaczenie jednej z grup przycisków powinno wyłączać drugą grupę.
   Jeżeli aktywujemy grupę *Push* dobrze byłoby zaznaczyć przycisk odpowiadający
   ostatniemu aktywnemu kanałowi. W tym celu trzeba uzupełnić funkcję ``ustawStan()``.
   Spróbuj użyć poniższego kodu:

.. highlight:: python
.. code-block:: python

            nadawca = self.sender()
            if nadawca.objectName() == 'Radio':
                self.grupaPBtn.setChecked(False)
            if nadawca.objectName() == 'Push':
                self.grupa_rb.setChecked(False)
                for btn in self.grupaP.buttons():
                    btn.setChecked(False)
                    if btn.text() in self.kanaly:
                        btn.setChecked(True)

Ponieważ w(y)łączanie ramek z przyciskami obsługujemy w jednym slocie,
musimy wiedzieć, która ramka wysłała sygnał. Metoda ``self.sender()``
zwraca nam nadawcę, a za pomocą metody ``objectName()`` możemy odczytać
jego nazwę.

Jeżeli ramką źródłową jest ta z przyciskami PushButton,
w pętli ``for btn in self.grupaP.buttons():`` na początku odznaczamy
każdy przycisk po to, żeby zaznaczyć go, o ile wskazywany przez niego
kanał jest w zbiorze.

3. Stan pól edycyjnych powinien odpowiadać stanowi przycisków PushButton,
   wciśnięty przycisk to aktywne pole i odwrotnie. Dopisz odpowiedni kod
   do slotu ``ustawKanalPBtn()``. Wykorzystaj funkcję ``getattr``,
   aby uzyskać dostęp do właściwego pola edycyjnego.

4. Funkcja ``zmienKolor()`` nie jest zabezpieczona przed błędnymi danymi
   wprowadzanymi do pól edycyjnych. Prześledź komunikaty w konsoli pojawiające
   się po wpisaniu wartości ujemnych, albo tekstu. Sytuacje takie można obsłużyć
   dopisując na początku funkcji np. taki kod:

.. highlight:: python
.. code-block:: python

        try:
            wartosc = int(wartosc)
        except ValueError:
            wartosc = 0
        if wartosc > 255:
            wartosc = 255

5. Jak zostało pokazane w aplikacji, nic nie stoi na przeszkodzie, żeby podobne
   sygnały obsługiwane były przez jeden slot. Niekiedy jednak wymaga to pewnych
   dodatkowych zabiegów. Można by na przykład spróbować połączyć sloty
   ``ustawKanalRBtn()`` i ``ustawKanalCBox()`` w jeden ``ustawKanal()``,
   który mógłby zostać zaimplementowany tak:

.. highlight:: python
.. code-block:: python

    def ustawKanal(self, wartosc):
        self.kanaly = set()  # resetujemy zbiór kanałów
        try:  # ComboBox
            if len(wartosc) == 1:
                self.kanaly.add(wartosc)
        except TypeError:  # RadioButton
            nadawca = self.sender()
            if wartosc:
                self.kanaly.add(nadawca.text())

6. Dodaj dwa osobne przyciski, które umożliwią kopiowanie koloru i kształtu z jednej figury
   na drugą.

Materiały
***************

1. `Qt Widgets <https://doc.qt.io/qt-6/qtwidgets-index.html>`_
2. `Widgets Tutorial <https://doc.qt.io/qt-6/widgets-tutorial.html>`_
3. `Layout Management <https://doc.qt.io/qt-6/layout.html>`_

**Źródła:**

* :download:`Widżety Qt5 <widzety_qt5.zip>`
