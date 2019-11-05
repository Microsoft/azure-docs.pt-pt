---
title: Aplicar Transformação SQL
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo aplicar transformação de SQL no serviço de Azure Machine Learning para executar uma consulta do SQLite em conjuntos de dados de entrada para transformá-los.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 62c61d589324fe8364fe4630b3cf2cc64e1860b1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493913"
---
# <a name="apply-sql-transformation"></a>Aplicar Transformação SQL

Este artigo descreve um módulo do designer de Azure Machine Learning (versão prévia).

Usando o módulo aplicar transformação SQL, você pode:
  
-   Crie tabelas para resultados e salve os conjuntos de dados em um banco de dados portátil.  
  
-   Execute transformações personalizadas em tipos de dados ou crie agregações.  
  
-   Executar instruções de consulta SQL para filtrar ou alterar dados e retornar os resultados da consulta como uma tabela de dados.  

> [!IMPORTANT]
> O mecanismo SQL usado neste módulo é **SQLite**. Para obter mais informações sobre a sintaxe do SQLite, consulte [SQL como compreendido pelo SQLite](https://www.sqlite.org/index.html) para obter mais informações.  

## <a name="how-to-configure-apply-sql-transformation"></a>Como configurar aplicar transformação SQL  

O módulo pode levar até três conjuntos de valores como entradas. Ao fazer referência aos conjuntos de dados conectados a cada porta de entrada, você deve usar os nomes `t1`, `t2`e `t3`. O número da tabela indica o índice da porta de entrada.  
  
O parâmetro restante é uma consulta SQL, que usa a sintaxe do SQLite. Ao digitar várias linhas na caixa de texto **script SQL** , use um ponto-e-vírgula para encerrar cada instrução. Caso contrário, as quebras de linha são convertidas em espaços.  

Este módulo dá suporte a todas as instruções padrão da sintaxe do SQLite. Para obter uma lista de instruções sem suporte, consulte a seção [observações técnicas](#technical-notes) .

##  <a name="technical-notes"></a>Notas técnicas  

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

-   Uma entrada é sempre necessária na porta 1.  
  
-   Para identificadores de coluna que contêm um espaço ou outros caracteres especiais, sempre coloque o identificador de coluna entre colchetes ou aspas duplas ao se referir à coluna nas cláusulas `SELECT` ou `WHERE`.  
  
### <a name="unsupported-statements"></a>Instruções sem suporte  

Embora o SQLite dê suporte a grande parte do padrão ANSI SQL, ele não inclui muitos recursos compatíveis com sistemas de banco de dados relacionais comerciais. Para obter mais informações, consulte [SQL como compreendido pelo SQLite](http://www.sqlite.org/lang.html). Além disso, esteja ciente das seguintes restrições ao criar instruções SQL:  
  
- O SQLite usa a digitação dinâmica para valores, em vez de atribuir um tipo a uma coluna como na maioria dos sistemas de banco de dados relacionais. Ele é digitado de forma fraca e permite a conversão implícita de tipo.  
  
- `LEFT OUTER JOIN` é implementado, mas não `RIGHT OUTER JOIN` ou `FULL OUTER JOIN`.  

- Você pode usar instruções `RENAME TABLE` e `ADD COLUMN` com o comando `ALTER TABLE`, mas não há suporte para outras cláusulas, incluindo `DROP COLUMN`, `ALTER COLUMN`e `ADD CONSTRAINT`.  
  
- Você pode criar uma exibição no SQLite, mas depois que as exibições forem somente leitura. Não é possível executar uma instrução `DELETE`, `INSERT`ou `UPDATE` em uma exibição. No entanto, você pode criar um gatilho que é acionado em uma tentativa de `DELETE`, `INSERT`ou `UPDATE` em uma exibição e executar outras operações no corpo do gatilho.  
  

Além da lista de funções não suportadas fornecida no site do SQLite oficial, o seguinte wiki fornece uma lista de outros recursos sem suporte: [SQLite-SQL sem suporte](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
