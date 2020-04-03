---
title: Unidades de Armazém de Dados (DWUs) em Azure Synapse Analytics (anteriormente SQL DW)
description: Recomendações sobre a escolha do número ideal de unidades de armazém de dados (DWUs) para otimizar o preço e o desempenho, e como alterar o número de unidades.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ace4bc2e46d9e1926da18dedb163657d4f343979
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586315"
---
# <a name="data-warehouse-units-dwus"></a>Unidades de Armazém de Dados (DWUs)

Recomendações sobre a escolha do número ideal de unidades de armazém de dados (DWUs) para otimizar o preço e o desempenho, e como alterar o número de unidades.

## <a name="what-are-data-warehouse-units"></a>O que são Unidades de Armazém de Dados

Uma [piscina Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) representa uma coleção de recursos analíticos que estão a ser aprovisionados. Os recursos analíticos são definidos como uma combinação de CPU, memória e IO. Estes três recursos são agregados em unidades de escala de cálculo chamadas Unidades de Armazém de Dados (DWUs). Uma DWU é uma medida abstrata e normalizada do desempenho e recursos de computação. Uma alteração ao nível do seu serviço altera o número de DWUs que estão disponíveis para o sistema, o que por sua vez ajusta o desempenho, e o custo, do seu sistema.

Para um desempenho mais elevado, pode aumentar o número de unidades de armazém de dados. Para menos desempenho, reduza as unidades de armazém de dados. Os custos de armazenamento e de computação são faturados em separado, pelo que a alteração das unidades do armazém de dados não afeta os custos de armazenamento.

O desempenho das unidades de armazém de dados baseia-se nestas métricas de carga de trabalho:

- A rapidez com que uma consulta padrão de armazenamento de dados pode digitalizar um grande número de linhas e, em seguida, executar uma agregação complexa. Esta operação é intensiva em I/O e CPU.
- A rapidez com que o armazém de dados pode ingerir dados de Blobs de Armazenamento Azure ou do Lago de Dados Azure. Esta operação é de rede e de CPU intensiva.
- A rapidez com que o [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) comando T-SQL pode copiar uma tabela. Esta operação envolve a leitura de dados do armazenamento, a sua distribuição pelos nós do aparelho e a escrita para armazenamento novamente. Esta operação é CPU, IO, e rede intensiva.

Crescente DWUs:

- Lialmost altera o desempenho do sistema para exames, agregações e declarações ctas
- Aumenta o número de leitores e escritores para operações de carga da PolyBase
- Aumenta o número máximo de consultas simultâneas e slots de condivisões.

## <a name="service-level-objective"></a>Objetivo de Nível de Serviço

O Objetivo de Nível de Serviço (SLO) é a definição de escalabilidade que determina o custo e o nível de desempenho do seu armazém de dados. Os níveis de serviço para o conjunto Gen2 SQL são medidos em unidades de armazém de dados (DWU), por exemplo DW2000c.

No T-SQL, a definição de SERVICE_OBJETIVE determina o nível de serviço para a sua piscina SQL.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Limites de capacidade

Cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma quota da Unidade de Transações de Base de [Dados (DTU)](../../sql-database/sql-database-service-tiers-dtu.md) que permite um número específico de unidades de armazém de dados. Para obter mais informações, consulte os limites de capacidade de gestão da [carga de trabalho.](sql-data-warehouse-service-capacity-limits.md#workload-management)

## <a name="how-many-data-warehouse-units-do-i-need"></a>Quantas unidades de armazém de dados preciso

O número ideal de unidades de armazém de dados depende muito da sua carga de trabalho e da quantidade de dados que carregou no sistema.

Passos para encontrar o melhor DWU para a sua carga de trabalho:

1. Comece por selecionar um DWU menor.
2. Monitorize o desempenho da sua aplicação à medida que testa as cargas de dados no sistema, observando o número de DWUs selecionados em comparação com o desempenho observado.
3. Identifique quaisquer requisitos adicionais para períodos periódicos de atividade máxima. As cargas de trabalho que mostram picos e calhas significativos em atividade podem ter de ser dimensionadas com frequência.

O SQL Analytics é um sistema de escala que pode fornecer grandes quantidades de dados computação e consulta. Para ver as suas verdadeiras capacidades de escala, especialmente em DWUs maiores, recomendamos a escala do conjunto de dados à medida que escala para garantir que tem dados suficientes para alimentar os CPUs. Para testes em escala, recomendamos a utilização de pelo menos 1 TB.

> [!NOTE]
>
> O desempenho da consulta só aumenta com mais paralelinização se o trabalho puder ser dividido entre nós de computação. Se descobrir que a escala não está a alterar o seu desempenho, poderá ter de afinar o design da sua tabela e/ou as suas consultas. Para obter orientação de afinação de consultas, consulte [Gerir as consultas do utilizador](cheat-sheet.md).

## <a name="permissions"></a>Permissões

A alteração das unidades de armazém de dados requer as permissões descritas na [BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-transact-sql).

Funções incorporadas para recursos Azure, tais como SQL DB Contributor e SQL Server Contributor podem alterar as definições de DWU.

## <a name="view-current-dwu-settings"></a>Ver as definições atuais do DWU

Para ver a definição atual de DWU:

1. Abra o Explorador de Objetos do Servidor SQL em Estúdio Visual.
2. Ligue-se à base de dados principal associada ao servidor lógico da Base de Dados SQL.
3. Selecione a partir da visão dinâmica de gestão sys.database_service_objetives. Segue-se um exemplo:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Alterar unidades de armazém de dados

### <a name="azure-portal"></a>Portal do Azure

Para alterar DWUs:

1. Abra o [portal Azure,](https://portal.azure.com)abra a sua base de dados e clique em **Escala**.

2. Em **Escala,** mova o slider para a esquerda ou para a direita para alterar a definição de DWU.

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **sim** para confirmar ou **não** para cancelar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para alterar os DWUs, utilize o [cmdlet Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell. O exemplo seguinte define o objetivo do nível de serviço para DW1000c para a base de dados MySQLDW que está hospedada no servidor MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Para mais informações, consulte [powerShell cmdlets para SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Com o T-SQL pode visualizar as definições dWU atuais, alterar as definições e verificar o progresso.

Para alterar os DWUs:

1. Ligue-se à base de dados principal associada ao seu servidor lógico de base de dados SQL.
2. Utilize a declaração TSQL da [ALTER DATABASE.](/sql/t-sql/statements/alter-database-transact-sql) O exemplo seguinte define o objetivo do nível de serviço para DW1000c para a base de dados MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>APIs REST

Para alterar os DWUs, utilize a API de Base de Dados de [Criação ou Atualização.](/rest/api/sql/databases/createorupdate) O exemplo seguinte define o objetivo do nível de serviço para DW1000c para a base de dados MySQLDW, que está hospedada no servidor MyServer. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

Para mais exemplos de API REST, consulte ASAP IS [REST para SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Verifique o estado das alterações da DWU

As alterações da DWU podem demorar vários minutos a ser concluídas. Se estiver a escalonar automaticamente, considere a implementação da lógica para garantir que determinadas operações foram concluídas antes de prosseguir com outra ação.

Verificar o estado da base de dados através de vários pontos finais permite-lhe implementar corretamente a automatização. O portal fornece notificação após a conclusão de uma operação e das bases de dados estado atual, mas não permite a verificação programática do Estado.

Não é possível verificar se há operações de escala com o portal Azure.

Para verificar o estado das alterações da DWU:

1. Ligue-se à base de dados principal associada ao seu servidor lógico de base de dados SQL.

1. Envie a seguinte consulta para verificar o estado da base de dados.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. Submeter a seguinte consulta para verificar o estado de funcionamento

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
Este DMV devolve informações sobre várias operações de gestão no seu pool SQL, como a operação e o estado da operação, que é IN_PROGRESS ou concluída.

## <a name="the-scaling-workflow"></a>O fluxo de trabalho de escala

Quando inicia uma operação à escala, o sistema primeiro mata todas as sessões abertas, refinanciando quaisquer transações abertas para garantir um estado consistente. Para operações de escala, a escala só ocorre após a conclusão desta reversão transacional.  

- Para uma operação de escala, o sistema desprende todos os nós de computação, provisões os nós de cálculo adicionais e, em seguida, volta a ligar-se à camada de armazenamento.
- Para uma operação de escala para baixo, o sistema desprende todos os nós de computação e, em seguida, volta a ligar apenas os nós necessários à camada de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre gestão do desempenho, consulte [as classes de recursos para gestão](resource-classes-for-workload-management.md) de carga de trabalho e limites de [memória e concurrency.](memory-concurrency-limits.md)
