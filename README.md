# MIDI-CV-Trig-Converter

# Zadání
Navrhněte a realizujte převodník rozhraní MIDI na řídicí signály analogových syntezátorů CV/Trig pro modulární systém Eurorack. Převodník bude umožňovat převod zpráv nota zapnuta/vypnuta, zpráv Pitch bend change a zpráv pro změnu kontroleru. Převodník bude disponovat polyfonními výstupy CV, Gate a Trig pro výšku tónu a stlačení klávesy a dalšími výstupy řídicího signálu CV pro kontrolery a bude umožňovat výběr MIDI kontrolru pro každý z těchto výstupů. Převodník bude také obsahovat výstupy Trig pro převod zpráv synchronizace MIDI Clock.

<img width="1492" height="670" alt="obrazek" src="https://github.com/user-attachments/assets/9568274c-023e-434f-b1ac-955999811ef4" />

# Popis
Z MIDI inputu půjdou data do UART portu na STM32 (31250 baud). Zapíše se Note On, Note Off, Výška tónu, Pitch bend a Clock. Note On, Note Off a výšky tónu se uloží pro každý hlas zvlášť.
Polyfonie se může řídit jako round robin nebo last note priority.

MIDI clock běží na 24ppqn, takže v STM32 bude potřeba vydělit clock rate (na čtvrťovou nebo osminovou notu, podle toho, jak bude potřeba).

Z MCU modelů nejvhodněji vypadá STM32F405RG + externí 16-bitové, čtyřkanálové převodníky (AD5685R).

Je ještě otázka, jak s výstupním napětím. Pro 20Hz - 20kHz je potřeba přibližně 10 oktáv. Nejčastěji jsou u oscilátorů standardy 0V až 10V, -5V až 5V a -3V až 7V. Nejjednodušší asi bude 0-10V. Jestli bude na výstupu převodníku 0,33V (nebo 0,5V), tak se dá potom požít zesilovač (OPA2277), který rozpětí dostane až na těch 10V. Za ním by pak byl ještě buffer (OPA1612) a pokud by bylo potřeba, tak ještě jednoduchý RC filtr na vyhlazení průběhu napětí a ochrané diody.

4 kanály V/Oct mohou jít přes jeden čtyřkanálový převodník AD5685R, druhý převodník by potom byl kombinovaný pro pitch bend a CV. OPA2277 a OPA1612 jsou oba dvoukanálové, takže by je teoreticky šlo použít po třech pro celkem 6 analogových kanálů (nejsem si jistý, jestli mají oba kanály dostatečně dobrou přesnost pro účely V/Oct signálu).

Gate a Trigger signály by mohly jít z GPIO a do MOSFETu 2N7002. MOSFETY by měly umožnit odebírat růyné namětí v závislosti na tom, jaké napětí je konkrétně potřeba na trigger/gate inputu na jiném modulu (většinou 5V/10V/12V).



