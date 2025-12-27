---
{"dg-publish":true,"dg-home":true,"permalink":"/Index/","tags":["gardenEntry"],"dgPassFrontmatter":true}
---

# Generování atomických poznámek pomocí AI
Při budování znalostních bází (Knowledge Management) je kritickým bodem škálovatelnost. Manuální propojování poznámek a udržování kontextu, jak to vyžaduje například metoda Zettelkasten, se s rostoucím objemem dat stává neudržitelným. Tento text popisuje technický experiment zaměřený na automatizaci tohoto procesu využitím agentových systémů.
Řešení je postaveno na platformě [n8n](https://n8n.io) a celá flow se skládá ze dvou logických celků:
- Prvním je LLM chain, který slouží k parsování nestrukturovaného textu (knihy, články) a extrakci klíčových slov, včetně jejich vysvětlení.
- Druhým je AI agent napojený na vektorovou databázi, který má za úkol projít všechny vygenerované poznámky a pomocí  sémantického vyhledávání (embeddings) identifikovat souvislostí mezi nově vzniklým obsahem a existující bází.
Flow je postavena na předpokladu, že uživatel pro poznámkování využívat [Obsidian](https://obsidian.md), jež se označuje jako tzv. Second brain. Vstupem pro flow jsou PDF soubory, které následně putují do LLM Chain, jež má za úkol extrahovat v rozmezí 5 - 50 atomických myšlenek s pravidly:
```
- poznámka by měla být dlouhá maximálně 100 - 300 slov
- Srozumitelná sama o sobě
- Konkrétní tvrzení nebo koncept
- Musí být vždy v češtině
- V případě víceslovných tagů, spoj je pomocí "_", aby z toho bylo jedno slov
```
Tento krok z důvodu rychlosti a přesnosti je prováděn pomocí API, konkrétně [OpenAI](https://openai.com) modelem GPT-5 mini. Model byl zvolen kvůli svému poměru ceny a výkonu.
V druhém kroku jsou jednotlivé poznámky postupně ukládání do vektorové databáze [Qdrant](https://qdrant.tech), pomocí lokálního embedding modelu Qwen3-embedding. Protože se jedná o atomické poznámky, tak nedochází k žádnému rozdělení poznámek. Každá poznámka v sobě nese metadata v podobě:
```
title
originalFile
tags
```
Tyto metadata napomáhají v třetím kroku AI agentovi, jež rovněž využívá mozek lokální model (Qwen3 8b) a jako znalostní bází čerpá z vytvořené vektorové databáze, díky ní je schopen z metadat vytvořit vztahy mezi pznámkami. AI agent má ve svém promptu nastaven úkoly:
```
1. Použij Qdrant Retrieve tool - vyhledej podobné poznámky k této poznámce
2. Vyfiltruj pouze poznámky se similarity > 0.80 (vysoká podobnost)
3. Ignoruj vstupní poznámku (pokud by se našla sama)
```
A striktně nastavená pravidla:
```
- Používej POUZE názvy z Qdrant metadata.title
- Pokud není žádná podobná poznámka (similarity > 0.80), vrať prázdné pole relatedNotes: []
- NIKDY nevymýšlej názvy poznámek
```
Flow byla testována na celkem třech typech materiál - kniha [[Atomic Habbits\|Atomic Habbits]],  [[Gang of Four Design Patterns in Machine Learning and Recommender Systems\|semestrálním projektu do předmětu Návrhové vzory]], odborných článků na téma [[Smart home\|Chytrá domácnost]].
Následující obrázek představuje průchod celé flow, jež pracuje s Google diskem pro automatizované načítání a ukládání souborů do složky s Obsidian trezorem.
![[n8n-flow.jpg\|n8n-flow.jpg]]