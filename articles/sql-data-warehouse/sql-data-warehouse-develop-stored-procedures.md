---
title: Usando procedimentos armazenados no Azure SQL Data Warehouse | Microsoft Docs
description: Dicas para implementar procedimentos armazenados no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2c12a679ed5f0a1574deb34df8c0151e737d2d01
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479596"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Usando procedimentos armazenados no SQL Data Warehouse
Dicas para implementar procedimentos armazenados no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="what-to-expect"></a>O que esperar

O SQL Data Warehouse dá suporte a muitos dos recursos do T-SQL que são usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução.

No entanto, para manter a escala e o desempenho de SQL Data Warehouse também há alguns recursos e funcionalidades que têm diferenças comportamentais e outras que não têm suporte.


## <a name="introducing-stored-procedures"></a>Introdução aos procedimentos armazenados
Os procedimentos armazenados são uma ótima maneira de encapsular seu código SQL; armazenando-o perto de seus dados no data warehouse. Os procedimentos armazenados ajudam os desenvolvedores a modularizar suas soluções encapsulando o código em unidades gerenciáveis; facilitando a maior Reusabilidade de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-los ainda mais flexíveis.

SQL Data Warehouse fornece uma implementação simplificada e simplificada de procedimento armazenado. A maior diferença em comparação com SQL Server é que o procedimento armazenado não é um código pré-compilado. Em data warehouses, o tempo de compilação é pequeno em comparação ao tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código do procedimento armazenado seja otimizado corretamente para consultas grandes. O objetivo é economizar horas, minutos e segundos, não milissegundos. Portanto, é mais útil considerar os procedimentos armazenados como contêineres para a lógica do SQL.     

Quando SQL Data Warehouse executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas em tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="nesting-stored-procedures"></a>Aninhando procedimentos armazenados
Quando procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, o procedimento armazenado interno ou a invocação de código é considerado aninhado.

O SQL Data Warehouse dá suporte a um máximo de oito níveis de aninhamento. Isso é um pouco diferente para SQL Server. O nível de aninhamento no SQL Server é 32.

A chamada de procedimento armazenado de nível superior equivale a aninhar o nível 1.

```sql
EXEC prc_nesting
```
Se o procedimento armazenado também fizer outra chamada EXEC, o nível de aninhamento aumentará para dois.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se o segundo procedimento executar um SQL dinâmico, o nível de aninhamento aumentará para três.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Observe que, no momento, o SQL Data Warehouse não dá suporte a [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Você precisa controlar o nível de aninhamento. É improvável que você exceda o limite de oito níveis de aninhamento, mas, se você fizer isso, precisará retrabalhar com seu código para ajustar os níveis de aninhamento dentro desse limite.

## <a name="insertexecute"></a>INSERIR.. EXECUTADOS
SQL Data Warehouse não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, há uma abordagem alternativa que você pode usar. Para obter um exemplo, consulte o artigo em [tabelas temporárias](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Limitações
Há alguns aspectos dos procedimentos armazenados Transact-SQL que não são implementados no SQL Data Warehouse.

São:

* procedimentos armazenados temporários
* procedimentos armazenados numerados
* procedimentos armazenados estendidos
* Procedimentos armazenados CLR
* opção de criptografia
* opção de replicação
* parâmetros com valor de tabela
* parâmetros somente leitura
* parâmetros padrão
* contextos de execução
* instrução de retorno

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

