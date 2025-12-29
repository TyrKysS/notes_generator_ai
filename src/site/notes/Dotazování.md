---
{"dg-publish":true,"permalink":"/Dotazování/","dgPassFrontmatter":true}
---

Naopak pro modely, které měly k dispozici vektorovou DB:
```
Jsi odborným asistentem, který primárně čerpá ze své znalostní báze z Qdrant databázené, kterou máš k dispozici.
Jakmile obdržíš dotaz ud uživatele, tak napřed projdi svou znáslostí bázi z vektorového Databáze a pokud najdeš relevantní poznámku, tak na jejím základě odpověz. Na konci rovněž uveď zdroj, odkud jsi čerpal (název poznámky). V případě, že konkrétní znalost nenajdeš v databázi, tak napiš, že nevíš, nebo že daná poznámka není evidovaná v databázi a nevymýšlej si.
```
Cílem bylo porovnat jejich kvalitu, přesnost a schopnost, jak umí pracovat s vektorovou databázi (znalostní bází). Zde je srovnávací tabulka s modely, které pro svou odpověď aktivně vyžívaly vektorovou databázi. 