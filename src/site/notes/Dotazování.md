---
{"dg-publish":true,"permalink":"/Dotazování/","dgPassFrontmatter":true}
---


# Dotazování LLM a vyhledávání ve vektorové DB
Kromě generování obsahu lze LLM využít k dotazování nad konkrétními poznámkami, jejich vysvětlování či vyhledávání. K tomuto účelu efektivně slouží vytvořená vektorová databáze. S cílem maximalizovat úsporu tokenů a umožnit běh na běžném PC byly pro srovnání vybrány čtyři lokální modely.
Každému modelu byly položeny tyto otázky:

1. Co je to agent? - Cílem bylo čerpat z [[Notes/Agent-based simulace\|Agent-based simulace]]
2. Co je to fog-cloud computing? - Cílem bylo čerpat z [[Notes/Fog a Cloud computing\|Fog a Cloud computing]]
3. Jaké senzory jsme schopni integrovat do Arduina? - Cílem bylo čerpat z [[Notes/Integrace senzorů a aktorů\|Integrace senzorů a aktorů]]
4. Jaká je aktuální cena modulu ESP32 na českém trhu? Cílem bylo odpovědět, že taková poznámka neexistuje, nebo že nemá přístup k těmto informacím.
5. Jaký je rozdíl mezi agentickou AI a AI agentem? - Cílem bylo nalézt a čerpat z [[Notes/Agentic AI\|Agentic AI]] a [[Notes/AI Agent\|AI Agent]]
6. Jak spolu souvisí AI agent a MAS? - Cílem bylo čerpat z [[Notes/AI Agent\|AI Agent]] a [[Notes/Multi-Agent Systems (MAS)\|Multi-Agent Systems (MAS)]]
7. Lze implementovat agenta do Arduino? Velká otevřenost, lze čerpat z [[Notes/AI Agent\|AI Agent]], [[Notes/Agent-based simulace\|Agent-based simulace]], [[Notes/Arduino Nano\|Arduino Nano]] a [[Notes/Arduino (mikrokontrolér)\|Arduino (mikrokontrolér)]]
8. Kdo víc potřebuje RAG? Agentická AI nebo AI agent? Lze čerpat z [[Notes/Retrieval-Augmented Generation (RAG)\|Retrieval-Augmented Generation (RAG)]], [[Notes/Agentic AI\|Agentic AI]] a [[Notes/AI Agent\|AI Agent]]

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