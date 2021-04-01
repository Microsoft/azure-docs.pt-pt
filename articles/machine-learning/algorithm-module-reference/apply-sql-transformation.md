---
title: Aplicar Transformação SQL
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de transformação DE SQL aplicar em Azure Machine Learning para executar uma consulta SQLite em conjuntos de dados de entrada para transformar os dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94555605"
---
# <a name="apply-sql-transformation"></a>Aplicar Transformação SQL

Este artigo descreve um módulo de Azure Machine Learning designer.

Utilizando o módulo de transformação DE Apply SQL, pode:
  
-   Crie tabelas para obter resultados e guarde os conjuntos de dados numa base de dados portátil.  
  
-   Realize transformações personalizadas em tipos de dados ou crie agregados.  
  
-   Execute declarações de consulta SQL para filtrar ou alterar dados e devolva os resultados da consulta como uma tabela de dados.  

> [!IMPORTANT]
> O motor SQL utilizado neste módulo é **SQLite.** Para obter mais informações sobre a sintaxe SQLite, consulte [SQL como entendido pela SQLite.](https://www.sqlite.org/index.html)
> Este módulo irá colidir com os dados para o SQLite, que está na memória DB, daí que a execução do módulo requer muito mais memória e pode atingir um `Out of memory` erro. Certifique-se de que o seu computador tem RAM suficiente.

## <a name="how-to-configure-apply-sql-transformation"></a>Como configurar Aplicar A Transformação SQL  

O módulo pode levar até três conjuntos de dados como entradas. Quando referenciar os conjuntos de dados ligados a cada porta de entrada, deve utilizar os nomes `t1` `t2` , e `t3` . O número da tabela indica o índice da porta de entrada.  

Segue-se o código de amostra para mostrar como juntar duas tabelas. t1 e t2 são dois conjuntos de dados ligados às portas de entrada esquerda e média da **Transformação Do SQL:**

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
O parâmetro restante é uma consulta SQL, que utiliza a sintaxe SQLite. Ao escrever várias linhas na caixa de texto **sql Script,** utilize um ponto e vírgula para terminar cada declaração. Caso contrário, as quebras de linha são convertidas em espaços.  

Este módulo suporta todas as declarações padrão da sintaxe SQLite. Para obter uma lista de declarações não apoiadas, consulte a secção [Notas Técnicas.](#technical-notes)

##  <a name="technical-notes"></a>Notas técnicas  

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

-   É sempre necessária uma entrada na porta 1.  
  
-   Para identificadores de colunas que contenham um espaço ou outros caracteres especiais, coloque sempre o identificador de coluna em suportes quadrados ou marcas de aspas duplas quando se refere à coluna nas `SELECT` cláusulas ou `WHERE` cláusulas.  
  
### <a name="unsupported-statements"></a>Declarações não apoiadas  

Embora a SQLite suporte grande parte da norma ANSI SQL, não inclui muitas funcionalidades suportadas por sistemas de base de dados relacionais comerciais. Para mais informações, consulte [o SQL como entendido pela SQLite.](http://www.sqlite.org/lang.html) Além disso, esteja ciente das seguintes restrições ao criar declarações SQL:  
  
- A SQLite utiliza dactilografia dinâmica para valores, em vez de atribuir um tipo a uma coluna como na maioria dos sistemas de base de dados relacionais. É fracamente dactilografado, e permite a conversão implícita do tipo.  
  
- `LEFT OUTER JOIN` é implementado, mas não `RIGHT OUTER JOIN` ou `FULL OUTER JOIN` . .  

- Pode usar `RENAME TABLE` e `ADD COLUMN` declarações com o `ALTER TABLE` comando, mas outras cláusulas não são apoiadas, incluindo `DROP COLUMN` , e `ALTER COLUMN` `ADD CONSTRAINT` .  
  
- Pode criar uma vista dentro do SQLite, mas depois as vistas são apenas para leitura. Não pode executar uma `DELETE` declaração ou uma declaração sobre uma `INSERT` `UPDATE` vista. No entanto, pode criar um gatilho que dispara numa tentativa `DELETE` `INSERT` de, ou `UPDATE` numa vista, e realizar outras operações no corpo do gatilho.  
  

Além da lista de funções não suportadas fornecidas no site oficial da SQLite, o seguinte wiki fornece uma lista de outras funcionalidades não apoiadas: [SQLite - SQL não suportado](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
