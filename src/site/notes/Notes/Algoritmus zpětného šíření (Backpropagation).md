---
{"dg-publish":true,"permalink":"/Notes/Algoritmus zpětného šíření (Backpropagation)/","dgPassFrontmatter":true}
---

Tagy
#backpropagation, #gradient_descent, #training

---
# Popis:
Backpropagation je metoda pro trénování vícevstupových neuronových sítí, která počítá gradienty chyby vzhledem k vahám pomocí pravidla o řetězení. Algoritmus šíří chybu od výstupu zpět přes vrstvy a aktualizuje váhy pomocí optimalizačních schémat, typicky gradientního sestupu. Díky tomu se síť postupně přizpůsobuje tak, aby minimalizovala ztrátovou funkci. Backpropagation je klíčový pro umožnění učení v hlubokých modelech a funguje jak pro lineární, tak nelineární aktivace; jeho výkon závisí na volbě učícího kroku, inicializace vah a regulárizačních technik.
# Reference
- [[Notes/Problém XOR\|Problém XOR]] (95%) - Problém XOR je klasický případ, na kterém se demonstruje schopnost vícevrstvých neuronových sítí naučit se nelineární vztahy – tedy přesně to, k čemu je zapotřebí algoritmus zpětného šíření (backpropagation). Tyto koncepty jsou přímo propojeny v oblasti strojového učení a neuronových sítí.

# Zdrojový soubor:
[[Vardakis et al. - 2022 - Smart Home Deep Learning as a Method for Machine .pdf]]