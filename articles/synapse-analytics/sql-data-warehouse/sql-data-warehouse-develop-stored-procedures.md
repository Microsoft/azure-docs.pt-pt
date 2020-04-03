---
title: Utilização de procedimentos armazenados
description: Dicas para desenvolver soluções implementando procedimentos armazenados na piscina SYnapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a8350f8027a78ae5692e12661f2e0d2013ab4c46
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618955"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Utilização de procedimentos armazenados na piscina SYnapse SQL
Este artigo fornece dicas para o desenvolvimento de soluções de piscina SQL implementando procedimentos armazenados.

## <a name="what-to-expect"></a>O que esperar

A piscina SQL suporta muitas das funcionalidades T-SQL que são usadas no Servidor SQL. Mais importante ainda, existem funcionalidades específicas de escala que pode usar para maximizar o desempenho da sua solução.

Além disso, para ajudá-lo a manter a escala e desempenho do pool SQL, existem funcionalidades e funcionalidades adicionais que têm diferenças comportamentais.


## <a name="introducing-stored-procedures"></a>Introdução de procedimentos armazenados
Os procedimentos armazenados são uma ótima maneira de encapsular o seu código SQL, que é armazenado perto dos dados do seu pool SQL. Os procedimentos armazenados também ajudam os desenvolvedores a modular as suas soluções encapsulando o código em unidades manejáveis, facilitando assim uma maior reutilização de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-los ainda mais flexíveis.

O pool SQL proporciona uma implementação simplificada e simplificada do procedimento armazenado. A maior diferença em comparação com o SQL Server é que o procedimento armazenado não é um código pré-compilado. 

Em geral, para os armazéns de dados, o tempo de compilação é pequeno em comparação com o tempo que demora a executar consultas contra grandes volumes de dados. É mais importante garantir que o código de procedimento armazenado está corretamente otimizado para grandes consultas. 

> [!TIP]
> O objetivo é poupar horas, minutos e segundos, não milissegundos. Então, é útil pensar nos procedimentos armazenados como recipientes para a lógica SQL.     

Quando a piscina SQL executa o seu procedimento armazenado, as declarações SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante este processo, cada declaração é convertida em consultas distribuídas. O código SQL que é executado contra os dados é diferente da consulta submetida.

## <a name="nesting-stored-procedures"></a>Procedimentos armazenados de nidificação
Quando os procedimentos armazenados, ligue para outros procedimentos armazenados, ou execute o SQL dinâmico, então o procedimento interno armazenado ou a invocação do código é dito estar aninhado.

A piscina SQL suporta um máximo de oito níveis de nidificação. Em contraste, o nível de ninho no SQL Server é 32.

A chamada de procedimento armazenada de nível superior equivale ao nível 1 do ninho.

```sql
EXEC prc_nesting
```
Se o procedimento armazenado também fizer outra chamada exec, o nível do ninho aumenta para dois.

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

A piscina SQL não suporta atualmente [.@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql) Como tal, é preciso rastrear o nível do ninho. É improvável que exceda o limite de oito ninhos. Mas, se o fizeres, precisas de reformular o teu código para encaixar os níveis de nidificação dentro deste limite.

## <a name="insertexecute"></a>INSERIR. EXECUTAR
A piscina SQL não permite consumir o conjunto de resultados de um procedimento armazenado com uma declaração INSERT. Há, no entanto, uma abordagem alternativa que pode usar. Por exemplo, consulte o artigo sobre [mesas temporárias.](sql-data-warehouse-tables-temporary.md) 

## <a name="limitations"></a>Limitações
Existem alguns aspetos dos procedimentos armazenados transact-SQL que não são implementados na piscina SQL, que são as seguintes:

* procedimentos temporários armazenados
* procedimentos armazenados numerados
* procedimentos armazenados alargados
* Procedimentos armazenados pela CLR
* opção de encriptação
* opção de replicação
* parâmetros de tabela
* parâmetros de leitura apenas
* parâmetros padrão
* contextos de execução
* declaração de retorno

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)

