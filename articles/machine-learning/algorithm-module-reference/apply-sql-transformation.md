---
title: Aplicar Transformação SQL
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de transformação SQL apply em Azure Machine Learning para executar uma consulta SQLite em conjuntos de dados de entrada para transformar os dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76314543"
---
# <a name="apply-sql-transformation"></a>Aplicar Transformação SQL

Este artigo descreve um módulo de designer de Machine Learning Azure (pré-visualização).

Utilizando o módulo de transformação SQL Apply, pode:
  
-   Crie tabelas para resultados e guarde os conjuntos de dados numa base de dados portátil.  
  
-   Realizar transformações personalizadas em tipos de dados ou criar agregados.  
  
-   Execute declarações de consulta SQL para filtrar ou alterar dados e devolver os resultados da consulta como uma tabela de dados.  

> [!IMPORTANT]
> O motor SQL utilizado neste módulo é **O SQLite**. Para obter mais informações sobre a sintaxe SQLite, consulte [o SQL como entendido pela SQLite](https://www.sqlite.org/index.html) para obter mais informações.  

## <a name="how-to-configure-apply-sql-transformation"></a>Como configurar A Transformação SQL  

O módulo pode levar até três conjuntos de dados como inputs. Quando se refere os conjuntos de dados ligados a `t1` `t2`cada `t3`porta de entrada, deve utilizar os nomes , e . O número da tabela indica o índice da porta de entrada.  
  
O parâmetro restante é uma consulta SQL, que usa a sintaxe SQLite. Ao digitar várias linhas na caixa de texto **do Script SQL,** utilize um ponto-semi-cólon para terminar cada declaração. Caso contrário, as quebras de linha são convertidas em espaços.  

Este módulo suporta todas as declarações padrão da sintaxe SQLite. Para obter uma lista de declarações não apoiadas, consulte a secção [Notas Técnicas.](#technical-notes)

##  <a name="technical-notes"></a>Notas técnicas  

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

-   É sempre necessária uma entrada na porta 1.  
  
-   Para identificadores de colunas que contenham um espaço ou outros caracteres especiais, encerre sempre o identificador de colunas em suportes quadrados ou marcas duplas quando se refere à coluna nas `SELECT` ou `WHERE` cláusulas.  
  
### <a name="unsupported-statements"></a>Declarações não apoiadas  

Embora a SQLite suporte grande parte da norma ANSI SQL, não inclui muitas funcionalidades suportadas por sistemas de base de dados relacionais comerciais. Para mais informações, consulte [o SQL como entendido pela SQLite](http://www.sqlite.org/lang.html). Além disso, esteja ciente das seguintes restrições ao criar declarações SQL:  
  
- O SQLite utiliza dactilografia dinâmica para valores, em vez de atribuir um tipo a uma coluna como na maioria dos sistemas de base de dados relacionais. É fracamente dactilografado, e permite a conversão implícita do tipo.  
  
- `LEFT OUTER JOIN`é implementado, mas `RIGHT OUTER JOIN` `FULL OUTER JOIN`não ou .  

- Pode utilizar `RENAME TABLE` `ADD COLUMN` e declarações com `ALTER TABLE` o comando, mas outras cláusulas não são suportadas, incluindo, `DROP COLUMN` `ALTER COLUMN`e `ADD CONSTRAINT`.  
  
- Pode criar uma vista dentro da SQLite, mas depois as vistas são apenas para leitura. Não pode `DELETE`executar `INSERT`uma, ou `UPDATE` declaração numa vista. No entanto, pode criar um gatilho `DELETE` `INSERT`que `UPDATE` dispara numa tentativa de, ou de uma vista e realizar outras operações no corpo do gatilho.  
  

Além da lista de funções não suportadas fornecidas no site oficial da SQLite, o seguinte wiki fornece uma lista de outras funcionalidades não suportadas: [SQLite - SQL não suportado](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
