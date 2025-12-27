---
{"dg-publish":true,"dg-home":true,"permalink":"/Index/","tags":["gardenEntry"],"dgPassFrontmatter":true}
---

# Generování atomických poznámek pomocí AI
Při budování znalostní báze (Knowledge Management) je kritickým bodem škálovatelnost. Manuální propojování poznámek a udržování kontextu, jak to vyžaduje například metoda Zettelkasten, se s rostoucím objemem dat stává neudržitelným a mohou snadno vznikat duplicity a nebo protichůdné poznámky.
Tento text popisuje technický experiment zaměřený na automatizaci při generování a propojení "chytrých" za pomocí agentového systému.
Řešení je postaveno na platformě [n8n](https://n8n.io) a celou flow lze rozdělit na dvě základní fáze:
1. Extrakce klíčových slov a jejich slovní popis.
2. Hledání vztahů mezi poznámkami a jejich propojení.
![Pasted image 20251227175833.png](/img/user/Pasted%20image%2020251227175833.png)
# Kroky pro generování poznámek
Pro extrakci pojmů je využit nód **Basic LLM Chain**. jehož vstupní prompt má za cíl na základě abstraktu představení a výsledků extrahovat klíčová slova a přiřadit jim vysvětlení. Prompt tak vypadá:
```
Jsi expertem na generování atomických poznámek. Jako vstup máš vědecký článek {{ $json.source_file }}.

Abstract:
{{ $json.abstract }}

Introduction:
{{ $json.introduction }}

Results:
{{ $json.results }}

Extrahuj 5-20 klíčových pojmů. Každý popiš 50-150 slov v češtině tak, aby byl atomický, neodkazoval se na původní text a jasně vysvětloval konkrétní pojem.

KRITICKY DŮLEŽITÉ - FORMÁT VÝSTUPU:
- Vrať POUZE validní JSON
- ŽÁDNÝ plain text
- ŽÁDNÉ číslování 1), 2), 3)
- ŽÁDNÉ markdown bloky ```
- Začni znakem { a skonči znakem }


Nyní extrahuj pojmy a vrať validní JSON
```
Poznámky jsou extrahovány za pomocí [OpenAI API]([https://openai.com), konkrétně modelu *GPT-5-mini*. Model byl zvolen z důvodu své vysoké rychlosti a nízkým nákladům. V mezičase, ještě před uložením, se kontroluje (a případně vytváří) kolekce pro vektorovou databázi *[Qdrant](https://qdrant.tech)*, do níž jsou následně ukládány jednotlivé poznámky, jež byly vygenerovány pomocí Basic LLM chain.
Embedding probíhá lokálně prostřednictvím *[Ollama](https://ollama.com/)* (voláno přes HTTP Request), s využitím modelu *bge-m3*. Jedná se o poměrně nový multijazyčný model, který je rychlý a generuje vektory o velikosti 1024 dimenzí.
Do vektorové databáze je následně ukládán název poznámky (title), vysvětlení (content), zdrojový soubor (sourceFile) a tagy vystihující danou poznámku (tags). Každá poznámka má své ID. V tomto případě byl zvolen ruční proces generování pomocí hashovací funkce **MD5** s cílem eliminovat duplicitní položky (zajistit, že se stejná poznámka nenahraje dvakrát).
```
[
    {
        "id": "{{ $('Crypto').item.json.data }}",
        "payload": {
            "title": "{{ $('Crypto').item.json.title }}",
            "content": "{{ $('Crypto').item.json.content }}",
            "sourceFile": "{{ $('Crypto').item.json.sourceFile }}",
            "tags": {{ JSON.stringify($('Crypto').item.json.tags) }}
        },
        "vector": {{ JSON.stringify($('Embedding').item.json.embedding) }}
    }
]
```
V druhé fázi AI agent za pomocí modelu GPT-4.1 postupně prochází jednotlivě vygenerované poznámky a nechává si je rovněž vytahovat z vektorové databáze včetně nejbližších sousedů. 
```
Jsi expert na propojování atomických poznámek v Zettelkasten systému.

VSTUPNÍ POZNÁMKA:
Název: {{ $('Crypto').item.json.title }}
Obsah: {{ $('Crypto').item.json.content }}
Tagy: {{ $('Crypto').item.json.tags }}

DOSTUPNÉ POZNÁMKY Z DATABÁZE:
{{ $json.result.points.map((p, i) => `${i+1}. "${p.payload.title}"`).join('\n') }}

ÚKOL:
Analyzuj poznámky ze seznamu výše a najdi ty, které jsou **tematicky propojené** se vstupní poznámkou.

KRITÉRIA:
- Sdílejí společné technologie, koncepty nebo domény
- Doplňují nebo rozšiřují vstupní poznámku
- Vyber maximálně 5 nejlepších propojení
- Seřaď podle relevance (nejvíce související první)

KRITICKÉ PRAVIDLO:
V poli relatedNotes[].title MUSÍŠ použít PŘESNĚ názvy ze seznamu "DOSTUPNÉ POZNÁMKY".
ZKOPÍRUJ celý řetězec včetně diakritiky.
NIKDY nevymýšlej vlastní názvy.
Pokud není souvislost, vrať prázdné pole relatedNotes: []
``` 
Na základě toho AI agent vyhodnotíP jejich relevanci a vytvoří propojení, jež dále odůvodní.
# Příklad
Flow byla testována na celkem na těchto článcích zaměřených primárně na chytrou domácnost, agentový přístup a Arduino:
- [[Amine et al. - 2018 - Smart Home Automation System based on Arduino.pdf]],
- [[Naing - 2019 - 56 Arduino Based Smart Home Automation System.pdf]],
- [[Vardakis et al. - 2022 - Smart Home Deep Learning as a Method for Machine .pdf]], 
- [[Zhao et al. - 2019 - BIM Sim3D Multi-Agent Human Activity Simulation .pdf]]).
# Problémy a jejich návrh řešení
- duplicita poznámek - v případě, zpracování dalších článků, v níž se opakují pojmy, začnou vznikat duplicity, které pro systém se tváří jako dvě odlišné poznámky. Řešení průběžná kontrola nejen vektorové databáze, ale také fyzických poznámek a její sjednocení pomocí LLM.
- Přístup k API službám (OpenAI) - lze řešit lokálně, ale z důvodu slabého HW je proto vhodné využívat API. Výsledky jsou mnohem rychlejší a přesnější.