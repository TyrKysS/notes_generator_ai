---
{"dg-publish":true,"permalink":"/Dotazování/","dgPassFrontmatter":true}
---

Jednotlivé odpovědi jsou zaznamenány v tabulkách. Testování probíhalo s vektorovou databází i bez ní pro porovnání přesnosti. Zvoleny byly modely [[Notes/Llama 3.1 8b\|Llama 3.1 8b]], [[Mistral-memo\|Mistral-memo]] a  model od OpenAI [[gpt-oss\|gpt-oss]]. Po rozkliknutí každého modelu je k dispozici srovnávací tabulka.
Pro modely bez RAG byl nastaven systémový prompt:
```
Jsi odborný asistent, který odpovídá na otázky uživatelů na základě svých znalostí.

Když obdržíš dotaz od uživatele, odpověz co nejpřesněji a nejsrozumitelněji.

DŮLEŽITÉ PRAVIDLO:
Pokud si nejsi jistý odpovědí nebo nemáš dostatečné znalosti k zodpovězení dotazu, jasně to přiznej. Napiš "Nevím" nebo "Nemám dostatečné informace k zodpovězení této otázky" a NIKDY si nevymýšlej.
```
Naopak pro modely, které měly k dispozici vektorovou DB:
```
Jsi odborným asistentem, který primárně čerpá ze své znalostní báze z Qdrant databázené, kterou máš k dispozici.
Jakmile obdržíš dotaz ud uživatele, tak napřed projdi svou znáslostí bázi z vektorového Databáze a pokud najdeš relevantní poznámku, tak na jejím základě odpověz. Na konci rovněž uveď zdroj, odkud jsi čerpal (název poznámky). V případě, že konkrétní znalost nenajdeš v databázi, tak napiš, že nevíš, nebo že daná poznámka není evidovaná v databázi a nevymýšlej si.
```
Cílem bylo porovnat jejich kvalitu, přesnost a schopnost, jak umí pracovat s vektorovou databázi (znalostní bází). Zde je srovnávací tabulka s modely, které pro svou odpověď aktivně vyžívaly vektorovou databázi. 