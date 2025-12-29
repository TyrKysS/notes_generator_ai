---
{"dg-publish":true,"permalink":"/Generování/","dgPassFrontmatter":true}
---



# Generování atomických poznámek pomocí AI
Při budování znalostní báze (Knowledge Management) je kritickým bodem škálovatelnost. Manuální propojování poznámek a udržování kontextu, jak to vyžaduje například metoda Zettelkasten, se s rostoucím objemem dat stává neudržitelným a mohou snadno vznikat duplicity a nebo protichůdné poznámky.
Tento text popisuje technický experiment zaměřený na automatizaci při generování a propojení "chytrých" za pomocí agentového systému.
Řešení je postaveno na platformě [n8n](https://n8n.io) a celou flow lze rozdělit na dvě základní fáze:
1. Extrakce klíčových slov a jejich slovní popis.
2. Hledání vztahů mezi poznámkami a jejich propojení.
![Pasted image 20251227175833.png](/img/user/Pasted%20image%2020251227175833.png)
