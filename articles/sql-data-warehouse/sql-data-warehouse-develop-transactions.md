---
title: Usando transações no Azure SQL Data Warehouse | Documentos da Microsoft
description: Sugestões para a implementação de transações para o desenvolvimento de soluções no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0b4ce6f4479552f42d32124149f64614b7e3cb70
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369501"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Usando transações no SQL Data Warehouse
Sugestões para a implementação de transações para o desenvolvimento de soluções no Azure SQL Data Warehouse.

## <a name="what-to-expect"></a>O que esperar
Como poderia esperar, o SQL Data Warehouse suporta transações como parte da carga de trabalho de armazém de dados. No entanto, para garantir que o desempenho do SQL Data Warehouse é mantido em escala algumas funcionalidades estão limitadas em comparação com o SQL Server. Este artigo destaca as diferenças e apresenta uma lista de outros. 

## <a name="transaction-isolation-levels"></a>Níveis de isolamento de transação
O SQL Data Warehouse implementa transações ACID. No entanto, o nível de isolamento do suporte transacional está limitado a READ UNCOMMITTED; Este nível não pode ser alterado. Se READ UNCOMMITTED for uma preocupação, pode implementar um número de métodos para evitar leituras sujas de dados de codificação. Mais populares métodos de utilizam CTAS e alternância de partição de tabela (muitas vezes conhecido como o padrão de janela deslizante) para impedir que os utilizadores da consulta de dados que ainda está a ser preparados. As vistas que pré-filtrar os dados também são uma abordagem popular.  

## <a name="transaction-size"></a>Tamanho da transação
Uma transação de modificação de dados individual tem um tamanho limitada. O limite é aplicado por distribuição. Por conseguinte, a alocação total pode ser calculada ao multiplicar o limite por contagem de distribuição. Para aproximado o número máximo de linhas na transação divida o limite máximo de distribuição, o tamanho total de cada linha. Para colunas de comprimento variável, considere colocar um comprimento de coluna médio, em vez de utilizar o tamanho máximo.

Na tabela a seguir as seguintes suposições foram feitas:

* Ocorreu uma distribuição uniforme de dados 
* O comprimento de linha média é de 250 bytes

## <a name="gen2"></a>Ger2

| [DWU](sql-data-warehouse-overview-what-is.md) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo de transação (GB) | # Linhas por distribuição | Máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Ger1

| [DWU](sql-data-warehouse-overview-what-is.md) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo de transação (GB) | # Linhas por distribuição | Máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

O limite de tamanho da transação é aplicado por transação ou a operação. Não é aplicada em todas as transações simultâneas. Portanto, cada transação tem permissão para gravar essa quantidade de dados no log. 

Para otimizar e minimizar a quantidade de dados escritos no registo, veja a [transações de melhores práticas](sql-data-warehouse-develop-best-practices-transactions.md) artigo.

> [!WARNING]
> O tamanho máximo de transações só pode ser obtido para HASH ou mesmo de tabelas round robin distribuídos em que é a propagação de dados. Se a transação é a escrita de dados de forma inclinada para as distribuições de, em seguida, o limite é provável que seja atingido antes do tamanho máximo de transações.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Estado da transação
SQL Data Warehouse utiliza a função XACT_STATE() para comunicar uma transação com falha através do valor -2. Este valor significa que a transação falhou e está marcada para reversão apenas.

> [!NOTE]
> A utilização de -2 pela função XACT_STATE para indicar uma falha na transação representa um comportamento diferente para o SQL Server. SQL Server utiliza o valor de -1 para representar uma transação foi. SQL Server pode tolerar alguns erros dentro de uma transação sem ter de ser marcado como foi. Por exemplo `SELECT 1/0` seria fazer com que um erro, mas não forçar uma transação num Estado de funcionamento foi. SQL Server também permite ao leituras na transação foi. No entanto, o SQL Data Warehouse não permitem-lhe fazê-lo. Se ocorrer um erro dentro de uma transação do SQL Data Warehouse entrará automaticamente o estado-2 e não será capaz de fazer qualquer selecione ainda mais instruções até que a instrução foi revertida. Portanto, é importante verificar que o código da aplicação para ver se ele usa XACT_STATE() à medida que talvez precise fazer modificações no código.
> 
> 

Por exemplo, no SQL Server poderá ver uma transação que é semelhante ao seguinte:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

O código anterior fornece a seguinte mensagem de erro:

Msg 111233, 16 de nível, o estado 1, linha 1 111233; A transação atual foi abortada, e quaisquer alterações pendentes tem foi revertidas. Causa: Uma transação num estado só de reversão não foi explicitamente revertida antes de uma DDL, o DML ou a instrução SELECT.

Não obtém a saída das funções ERROR_ *.

No SQL Data Warehouse, o código precisa ser ligeiramente alterado:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Agora é observado o comportamento esperado. O erro na transação é gerido e as funções ERROR_ * fornecem valores, conforme o esperado.

Tudo o que mudou é que a reversão da transação tinha que ocorrer antes da leitura das informações de erro no bloco CATCH.

## <a name="errorline-function"></a>Função de Error_Line()
Também vale a pena observar que o SQL Data Warehouse não implementar ou compatíveis com a função ERROR_LINE(). Se tiver em seu código, terá de removê-lo para estar em conformidade com o SQL Data Warehouse. Utilize etiquetas de consulta no seu código em vez disso, para implementar a funcionalidade equivalente. Para obter mais detalhes, consulte a [etiqueta](sql-data-warehouse-develop-label.md) artigo.

## <a name="using-throw-and-raiserror"></a>Usando THROW e RAISERROR
THROW é a implementação de mais moderna para a desencadear exceções no SQL Data Warehouse mas RAISERROR também é suportado. Existem algumas diferenças que vale a pena prestar atenção para entretanto.

* Números de mensagens de erro definidas pelo utilizador não podem estar no intervalo de 100 000-150 000 para THROW
* Mensagens de erro RAISERROR são fixa em 50 000
* Não é suportada a utilização de sys

## <a name="limitations"></a>Limitações
O SQL Data Warehouse tem algumas restrições que se relacionam com transações.

Estas são as seguintes:

* Não existem transações distribuídas
* Não existem transações aninhadas permitidas
* Não guarde pontos permitido
* Não existem transações com nome
* Não existem transações marcadas
* Não há suporte para DDL, como criar tabela dentro de uma transação definidas pelo utilizador

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a otimização de transações, consulte [transações de melhores práticas](sql-data-warehouse-develop-best-practices-transactions.md). Para saber mais sobre outras práticas recomendadas do SQL Data Warehouse, veja [práticas recomendadas do SQL Data Warehouse](sql-data-warehouse-best-practices.md).

