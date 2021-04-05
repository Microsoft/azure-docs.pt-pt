---
title: Utilização de procedimentos armazenados
description: Dicas para o desenvolvimento de soluções implementando procedimentos armazenados para piscinas SQL dedicadas em Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673591"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Utilização de procedimentos armazenados para piscinas SQL dedicadas em Azure Synapse Analytics

Este artigo fornece dicas para o desenvolvimento de soluções de piscinas SQL dedicadas, implementando procedimentos armazenados.

## <a name="what-to-expect"></a>O que esperar

A piscina dedicada SQL suporta muitas das funcionalidades T-SQL que são usadas no SQL Server. Mais importante ainda, existem funcionalidades específicas de escala que podes usar para maximizar o desempenho da tua solução.

Além disso, para ajudá-lo a manter a escala e o desempenho de uma piscina DE SQL dedicada, existem funcionalidades e funcionalidades adicionais que têm diferenças comportamentais.

## <a name="introducing-stored-procedures"></a>Introdução de procedimentos armazenados

Os procedimentos armazenados são uma ótima maneira de encapsular o seu código SQL, que é armazenado perto dos seus dados dedicados do pool SQL. Os procedimentos armazenados também ajudam os desenvolvedores a modular as suas soluções, encapsulando o código em unidades manejáveis, facilitando assim uma maior reutilização de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-los ainda mais flexíveis.

O pool dedicado SQL proporciona uma implementação simplificada e simplificada do procedimento armazenado. A maior diferença em relação ao SQL Server é que o procedimento armazenado não é código pré-compilado.

Em geral, para os armazéns de dados, o tempo de compilação é pequeno em comparação com o tempo que leva a executar consultas contra grandes volumes de dados. É mais importante garantir que o código de procedimento armazenado está corretamente otimizado para grandes consultas.

> [!TIP]
> O objetivo é poupar horas, minutos e segundos, não milissegundos. Então, é útil pensar em procedimentos armazenados como recipientes para a lógica SQL.

Quando uma piscina SQL dedicada executa o seu procedimento armazenado, as declarações SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante este processo, cada declaração é convertida em consultas distribuídas. O código SQL que é executado contra os dados é diferente da consulta submetida.

## <a name="nesting-stored-procedures"></a>Procedimentos armazenados de nidificação

Quando os procedimentos armazenados ligam para outros procedimentos armazenados, ou executam SQL dinâmico, então o procedimento interno armazenado ou invocação de código é dito estar aninhado.

A piscina de SQL dedicada suporta um máximo de oito níveis de nidificação. Em contraste, o nível de ninho no SQL Server é de 32.

A chamada de procedimento armazenado de nível superior equivale ao nível do ninho 1.

```sql
EXEC prc_nesting
```

Se o procedimento armazenado também fizer outra chamada DOEC, o nível do ninho aumenta para dois.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Se o segundo procedimento executar algum SQL dinâmico, o nível do ninho aumenta para três.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Piscina dedicada SQL não suporta atualmente [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Como tal, tens de seguir o nível do ninho. É improvável que ultrapasse o limite do nível de oito ninhos. Mas, se o fizer, tem de reformular o seu código para se adaptar aos níveis de nidificação dentro deste limite.

## <a name="insertexecute"></a>INSERT..EXEBONITO

Piscina SQL dedicada não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma declaração INSERT. Há, no entanto, uma abordagem alternativa que pode utilizar. Por exemplo, consulte o artigo sobre [tabelas temporárias.](sql-data-warehouse-tables-temporary.md)

## <a name="limitations"></a>Limitações

Existem alguns aspetos dos procedimentos armazenados transact-SQL que não são implementados em piscinas SQL dedicadas, que são as seguintes:

* procedimentos temporários armazenados
* procedimentos armazenados numerados
* procedimentos armazenados alargados
* Procedimentos armazenados do CLR
* opção de encriptação
* opção de replicação
* parâmetros valorizados pela tabela
* parâmetros apenas de leitura
* parâmetros padrão
* contextos de execução
* declaração de retorno

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
