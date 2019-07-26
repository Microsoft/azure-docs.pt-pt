---
title: Usando transações no Azure SQL Data Warehouse | Microsoft Docs
description: Dicas para implementar transações no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7f00f8a25d0abf3af6d76b372b44145546a79879
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479615"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Usando transações no SQL Data Warehouse
Dicas para implementar transações no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="what-to-expect"></a>O que esperar
Como esperado, SQL Data Warehouse dá suporte a transações como parte da carga de trabalho de data warehouse. No entanto, para garantir que o desempenho do SQL Data Warehouse seja mantido em escala, alguns recursos são limitados em comparação com SQL Server. Este artigo destaca as diferenças e lista os outros. 

## <a name="transaction-isolation-levels"></a>Níveis de isolamento da transação
SQL Data Warehouse implementa transações ACID. No entanto, o nível de isolamento do suporte transacional é limitado à leitura não confirmada; Este nível não pode ser alterado. Se a leitura não confirmada for uma preocupação, você poderá implementar vários métodos de codificação para evitar leituras sujas de dados. Os métodos mais populares usam CTAS e a alternância de partição de tabela (geralmente conhecida como padrão de janela deslizante) para impedir que os usuários consultem dados que ainda estão sendo preparados. Exibições que filtram previamente os dados também são uma abordagem popular.  

## <a name="transaction-size"></a>Tamanho da transação
Uma única transação de modificação de dados tem tamanho limitado. O limite é aplicado por distribuição. Portanto, a alocação total pode ser calculada multiplicando o limite pela contagem de distribuição. Para aproximar o número máximo de linhas na transação, divida a extremidade de distribuição pelo tamanho total de cada linha. Para colunas de comprimento variável, considere pegar um comprimento médio de coluna em vez de usar o tamanho máximo.

Na tabela abaixo, foram feitas as seguintes suposições:

* Ocorreu uma distribuição uniforme de dados 
* O comprimento médio da linha é de 250 bytes

## <a name="gen2"></a>Ger2

| [DWU](sql-data-warehouse-overview-what-is.md) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo da transação (GB) | N º de linhas por distribuição | Máximo de linhas por transação |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18,75 |60 |1125 |75 milhões |4\.500.000.000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37,5 |60 |2\.250 |150 milhões |9\.000.000.000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56,25 |60 |3\.375 |225 milhões |13.500.000.000 |
| DW10000c |75 |60 |4\.500 |300,000,000 |18.000.000.000 |
| DW15000c |112,5 |60 |6\.750 |450 milhões |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900,000,000 |54.000.000.000 |

## <a name="gen1"></a>Ger1

| [DWU](sql-data-warehouse-overview-what-is.md) | Limite por distribuição (GB) | Número de distribuições | Tamanho máximo da transação (GB) | N º de linhas por distribuição | Máximo de linhas por transação |
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

O limite de tamanho de transação é aplicado por transação ou operação. Ele não é aplicado em todas as transações simultâneas. Portanto, cada transação tem permissão para gravar essa quantidade de dados no log. 

Para otimizar e minimizar a quantidade de dados gravados no log, veja o artigo [práticas recomendadas de transações](sql-data-warehouse-develop-best-practices-transactions.md) .

> [!WARNING]
> O tamanho máximo da transação só pode ser obtido para tabelas distribuídas de HASH ou ROUND_ROBIN em que a disseminação dos dados é uniforme. Se a transação estiver gravando dados de maneira distorcido para as distribuições, o limite provavelmente será alcançado antes do tamanho máximo da transação.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Estado da transação
SQL Data Warehouse usa a função XACT_STATE () para relatar uma transação com falha usando o valor-2. Esse valor significa que a transação falhou e está marcada apenas para reversão.

> [!NOTE]
> O uso de-2 pela função XACT_STATE para denotar uma transação com falha representa um comportamento diferente para SQL Server. SQL Server usa o valor-1 para representar uma transação não confirmável. SQL Server pode tolerar alguns erros dentro de uma transação sem ter que ser marcado como não confirmável. Por exemplo `SELECT 1/0` , causaria um erro, mas não forçaria uma transação em um estado não confirmado. SQL Server também permite leituras na transação não confirmável. No entanto, SQL Data Warehouse não permite que você faça isso. Se ocorrer um erro dentro de uma SQL Data Warehouse transação, ele entrará automaticamente no estado-2 e você não poderá fazer mais instruções SELECT até que a instrução tenha sido revertida. Portanto, é importante verificar se o código do aplicativo para ver se ele usa XACT_STATE (), pois talvez seja necessário fazer modificações no código.
> 
> 

Por exemplo, em SQL Server você pode ver uma transação semelhante à seguinte:

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

MSG 111233, nível 16, estado 1, linha 1 111233; A transação atual foi anulada e todas as alterações pendentes foram revertidas. Motivo: Uma transação em um estado somente reversão não foi explicitamente revertida antes de uma instrução DDL, DML ou SELECT.

Você não obterá a saída das funções ERROR_ *.

Em SQL Data Warehouse o código precisa ser ligeiramente alterado:

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

O comportamento esperado agora é observado. O erro na transação é gerenciado e as funções ERROR_ * fornecem valores conforme o esperado.

Tudo o que mudou é que a reversão da transação tinha que acontecer antes da leitura das informações de erro no bloco CATCH.

## <a name="errorline-function"></a>Função Error_Line ()
Também vale a pena observar que SQL Data Warehouse não implementa nem dá suporte à função ERROR_LINE (). Se você tiver isso em seu código, precisará removê-lo para estar em conformidade com SQL Data Warehouse. Use rótulos de consulta em seu código em vez de implementar a funcionalidade equivalente. Para obter mais detalhes, consulte o artigo [rótulo](sql-data-warehouse-develop-label.md) .

## <a name="using-throw-and-raiserror"></a>Usando THROW e RAISERROR
THROW é a implementação mais moderna para gerar exceções em SQL Data Warehouse, mas também há suporte para RAISERROR. No entanto, há algumas diferenças que valem a pena prestar atenção.

* Números de mensagens de erro definidas pelo usuário não podem estar no intervalo 100.000-150.000 para THROW
* As mensagens de erro RAISERROR são corrigidas às 50.000
* Não há suporte para o uso de sys. messages

## <a name="limitations"></a>Limitações
SQL Data Warehouse tem algumas outras restrições relacionadas às transações.

Eles são os seguintes:

* Nenhuma transação distribuída
* Nenhuma transação aninhada permitida
* Não há pontos de salvamento permitidos
* Nenhuma transação nomeada
* Nenhuma transação marcada
* Não há suporte para DDL, como CREATE TABLE dentro de uma transação definida pelo usuário

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como otimizar as transações, confira [práticas recomendadas de transações](sql-data-warehouse-develop-best-practices-transactions.md). Para saber mais sobre outras SQL Data Warehouse práticas recomendadas, consulte [SQL data warehouse práticas recomendadas](sql-data-warehouse-best-practices.md).

