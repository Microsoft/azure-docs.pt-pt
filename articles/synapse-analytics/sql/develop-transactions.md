---
title: Utilizar transações
description: Dicas para implementar transações com piscina DE SQL dedicada em Azure Synapse Analytics para o desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a2597a4bc6c5ed44f0e0050be3f69d7e840665e5
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323851"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Utilize transações com piscina SQL dedicada em Azure Synapse Analytics

Dicas para implementar transações com piscina DE SQL dedicada em Azure Synapse Analytics para o desenvolvimento de soluções.

## <a name="what-to-expect"></a>O que esperar

Como seria de esperar, o pool de SQL dedicado suporta transações como parte da carga de trabalho do armazém de dados. No entanto, para garantir que o desempenho do pool DE SQL dedicado é mantido à escala, algumas funcionalidades são limitadas quando comparadas com o SQL Server. Este artigo destaca as diferenças e lista os outros.

## <a name="transaction-isolation-levels"></a>Níveis de isolamento de transações

A piscina SQL implementa transações acid. O nível de isolamento do suporte transacional é incumpridor de READ UNCOMMITTEDED.  Pode alterá-lo para READ COMMITTED SNAPSHOT ISOLATION, ligando a READ_COMMITTED_SNAPSHOT opção de base de dados para uma base de dados do utilizador quando ligado à base de dados principal.  

Uma vez ativadas, todas as transações nesta base de dados são executadas sob LEITURA DE ISOLAMENTO INSTANTÂNEO E a definição de LEITURA NÃO COMPROMETIDA ao nível da sessão não será honrada. Consulte [as opções ALTER DATABASE SET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest&preserve-view=true) para obter mais informações.

## <a name="transaction-size"></a>Tamanho da transação
Uma única transação de modificação de dados é limitada em tamanho. O limite é aplicado por distribuição. Como tal, a atribuição total pode ser calculada multiplicando o limite pela contagem de distribuição. 

Para aproximar o número máximo de linhas na transação divida a tampa de distribuição pelo tamanho total de cada linha. Para colunas de comprimento variável, considere tomar um comprimento médio da coluna em vez de usar o tamanho máximo.

No quadro abaixo foram apresentados os seguintes pressupostos:

* Ocorreu uma distribuição uniforme de dados
* O comprimento médio da linha é de 250 bytes

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Tampa por distribuição (GB) | Número de Distribuições | Tamanho da transação MAX (GB) | # Linhas por distribuição | Max Rows por transação |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Tampa por distribuição (GB) | Número de Distribuições | Tamanho da transação MAX (GB) | # Linhas por distribuição | Max Rows por transação |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

O limite de tamanho da transação é aplicado por transação ou operação. Não é aplicado em todas as transações simultâneas. Portanto, cada transação é autorizada a escrever esta quantidade de dados para o registo.

Para otimizar e minimizar a quantidade de dados escritos no registo, consulte o artigo [de Boas Práticas de Transações.](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

> [!WARNING]
> O tamanho máximo da transação só pode ser alcançado para hash ou ROUND_ROBIN tabelas distribuídas onde a disseminação dos dados é uniforme. Se a transação estiver a escrever dados de forma distorcida para as distribuições, então é provável que o limite seja atingido antes do tamanho máximo da transação.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Estado de transação

O pool SQL utiliza a função XACT_STATE() para reportar uma transação falhada utilizando o valor -2. Este valor significa que a transação falhou e está marcada apenas para reversão.

> [!NOTE]
> A utilização de -2 pela função XACT_STATE para denotar uma transação falhada representa um comportamento diferente para o SQL Server. O SQL Server utiliza o valor -1 para representar uma transação incomitável. O SQL Server pode tolerar alguns erros dentro de uma transação sem ter de ser marcado como incomitável. Por `SELECT 1/0` exemplo, provocaria um erro, mas não forçaria uma transação a um estado incomitvel. O SQL Server também permite leituras na transação não comprometedora. No entanto, a piscina SQL dedicada não permite que você faça isso. Se ocorrer um erro dentro de uma transação dedicada do pool SQL, entrará automaticamente no estado -2 e não poderá fazer mais declarações selecionadas até que a declaração seja revirada. Por isso, é importante verificar se o seu código de aplicação para ver se utiliza XACT_STATE() como pode ser necessário fazer modificações de código.

Por exemplo, no SQL Server poderá ver uma transação que se pareça com o seguinte:

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

O código anterior apresenta a seguinte mensagem de erro:

Msg 111233, Nível 16, Estado 1, Linha 1 111233; A transação atual abortou e quaisquer alterações pendentes foram revoadas. Causa: Uma transação num estado apenas de reversão não foi explicitamente revertida antes de uma declaração de DDL, DML ou SELECT.

Não vai conseguir a saída das funções ERROR_*.

Na piscina SQL dedicada, o código precisa de ser ligeiramente alterado:

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
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
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

O comportamento esperado é agora observado. O erro na transação é gerido e as funções ERROR_* fornecem valores como esperado.

Tudo o que mudou foi que o ROLLBACK da transação tinha de acontecer antes da leitura das informações de erro no bloco CATCH.

## <a name="error_line-function"></a>Error_Line() função

Vale também a pena notar que o pool de SQL dedicado não implementa nem suporta a função ERROR_LINE(). Se tiver esta função no seu código, tem de removê-la para estar em conformidade com a piscina SQL dedicada. Utilize etiquetas de consulta no seu código para implementar funcionalidades equivalentes. Para mais informações, consulte o artigo [LABEL.](develop-label.md)

## <a name="use-of-throw-and-raiserror"></a>Utilização de THROW e RAISERROR

THROW é a implementação mais moderna para aumentar exceções em piscinas SQL dedicadas, mas RAISERROR também é apoiado. No entanto, há algumas diferenças que merecem ser prestas em atenção.

* Os números de mensagens de erro definidas pelo utilizador não podem estar na gama de 100.000 - 150.000 para THROW
* As mensagens de erro RAISERROR são fixadas em 50.000
* A utilização de sys.messages não é suportada

## <a name="limitations"></a>Limitações

O pool SQL tem algumas outras restrições relacionadas com transações. São os seguintes:

* Sem transações distribuídas
* Não são permitidas transações aninhadas
* Não são permitidos pontos de poupança
* Sem transações nomeadas
* Sem transações marcadas
* Sem suporte para DDL, como CREATE TABLE dentro de uma transação definida pelo utilizador

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a otimização de transações, consulte [as melhores práticas de Transações.](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Guias adicionais de boas práticas também são fornecidos para [piscina SQL](best-practices-sql-pool.md) e [piscina SQL sem servidor (pré-visualização)](best-practices-sql-on-demand.md).
