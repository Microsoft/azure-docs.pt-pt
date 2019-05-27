---
title: Uso de procedimentos armazenados no armazém de dados SQL do Azure | Documentos da Microsoft
description: Sugestões para a implementação de procedimentos armazenados no armazém de dados SQL do Azure para o desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 200433d95d62edf2e878e58e5089a6baff290775
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850575"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Uso de procedimentos armazenados no SQL Data Warehouse
Sugestões para a implementação de procedimentos armazenados no armazém de dados SQL do Azure para o desenvolvimento de soluções.

## <a name="what-to-expect"></a>O que esperar

O SQL Data Warehouse oferece suporte a muitos dos recursos de T-SQL que são utilizados no SQL Server. Mais importante, existem recursos específicos de escalamento horizontal que pode utilizar para maximizar o desempenho da sua solução.

No entanto, para manter o dimensionamento e desempenho do armazém de dados do SQL aqui estão alguns recursos e funcionalidades que têm diferenças de comportamento e outros que não são suportadas.


## <a name="introducing-stored-procedures"></a>Introdução ao procedimentos armazenados
Procedimentos armazenados são uma excelente forma de encapsular o código SQL; armazenando-perto dos seus dados no armazém de dados. Procedimentos armazenados ajudam os desenvolvedores modularizar suas soluções, encapsulando o código em unidades geríveis; facilitação de maior capacidade de reutilização de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-los ainda mais flexível.

O SQL Data Warehouse fornece uma implementação simplificada e simplificado de procedimento armazenado. A maior diferença em comparação comparada o SQL Server é que o procedimento armazenado não é códigos pré-compilados. Armazéns de dados, o tempo de compilação é pequeno em comparação com o tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código de procedimento armazenado corretamente é otimizado para consultas grandes. O objetivo é poupar horas, minutos e segundos, não milissegundos. Portanto, é mais útil pensar em procedimentos armazenados como contentores para a lógica SQL.     

Quando o SQL Data Warehouse executar seu procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas em tempo de execução. Durante este processo, cada instrução é convertida em consultas distribuídas. O código SQL que é executado em relação os dados é diferente de consulta submetida.

## <a name="nesting-stored-procedures"></a>Procedimentos armazenado de aninhamento
Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executar o SQL dinâmico, interno, em seguida, procedimento armazenado ou invocação de código-se que ser aninhados.

O SQL Data Warehouse suporta um máximo de oito níveis de aninhamento. Isso é ligeiramente diferente para o SQL Server. O nível de aninhamento no SQL Server é 32.

Equivale a chamada de procedimento armazenado de nível superior para aninhar nível 1.

```sql
EXEC prc_nesting
```
Se o procedimento armazenado também faz outra EXEC chamar, aumenta o nível de aninhamento para dois.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se o segundo procedimento, em seguida, executar alguns SQL dinâmico, aumenta o nível de aninhamento como três.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Tenha em atenção de que o SQL Data Warehouse não suporta atualmente [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Precisa controlar o nível de aninhamento. É improvável que exceda o limite de nível de aninhamento de oito, mas se o fizer, terá de Refazer seu código de acordo com os níveis de aninhamento deste limite.

## <a name="insertexecute"></a>INSERT.... EXECUTAR
O SQL Data Warehouse não permite que para consumir o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, há uma abordagem alternativa, que pode utilizar. Por exemplo, consulte o artigo sobre [tabelas temporárias](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Limitações
Existem alguns aspetos dos procedimentos armazenados do Transact-SQL que não são implementados no SQL Data Warehouse.

São:

* procedimentos armazenados temporários
* procedimentos armazenados numerados
* Procedimentos armazenados expandidos
* Procedimentos CLR armazenados
* Opção de encriptação
* opção de replicação
* parâmetros de valor de tabela
* parâmetros de só de leitura
* parâmetros de predefinição
* contextos de execução
* instrução Return

## <a name="next-steps"></a>Passos Seguintes
Para obter mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento](sql-data-warehouse-overview-develop.md).

