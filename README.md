# MIDI-CV-Trig-Converter

# Zadání
Navrhněte a realizujte převodník rozhraní MIDI na řídicí signály analogových syntezátorů CV/Trig pro modulární systém Eurorack. Převodník bude umožňovat převod zpráv nota zapnuta/vypnuta, zpráv Pitch bend change a zpráv pro změnu kontroleru. Převodník bude disponovat polyfonními výstupy CV, Gate a Trig pro výšku tónu a stlačení klávesy a dalšími výstupy řídicího signálu CV pro kontrolery a bude umožňovat výběr MIDI kontrolru pro každý z těchto výstupů. Převodník bude také obsahovat výstupy Trig pro převod zpráv synchronizace MIDI Clock.


![Diagram bez názvu](https://github.com/user-attachments/assets/7c10754c-8b78-4bd6-ba18-ec19210b17d3)


# Popis
Z MIDI inputu půjdou data do UART portu na STM32 (31250 baud). Zapíše se Note On, Note Off, Výška tónu, Pitch bend a Clock. Note On, Note Off a výšky tónu se uloží pro každý hlas zvlášť.
Polyfonie se může řídit jako round robin nebo last note priority.

Switchem se dají přepínat režimy:
1. 4 hlasy polyfonie a žádný výstup CV z MIDI CC
2. Monofonie a 3 výstupy CV z MIDI CC

MIDI clock běží na 24ppqn, takže v STM32 bude potřeba vydělit clock rate (na čtvrťovou nebo osminovou notu, podle toho, jak bude potřeba).

Z MCU modelů nejvhodněji vypadá STM32F405RG + 2 externí 12-bitové, dvoukanálové převodníky (MCP4922).

Rozsah CV a V/Oct bude 0 - 10 V, přičemž pitch bend se bude přičítat k výstupu, takže to se bude řídit softwarově.
Jestli bude na výstupu převodníku 0,33V (nebo 0,5V), tak se dá potom použít ještě buffer (OPA1612) a pokud by bylo potřeba, tak ještě jednoduchý RC filtr na vyhlazení průběhu napětí a ochranné diody.

Gate signály by mohly jít z GPIO a do MOSFETu 2N7002. MOSFETY by měly umožnit odebírat růyné namětí v závislosti na tom, jaké napětí je konkrétně potřeba na trigger/gate inputu na jiném modulu (většinou 5V/10V/12V).





