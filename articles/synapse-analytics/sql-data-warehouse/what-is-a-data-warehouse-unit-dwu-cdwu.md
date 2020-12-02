---
title: Unidades de Armazém de Dados (DWUs) para piscina SQL dedicada (anteriormente SQL DW)
description: Recomendações sobre a escolha do número ideal de unidades de armazém de dados (DWUs) para otimizar o preço e o desempenho, e como alterar o número de unidades.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d9d5d4009ad40eecee26271b726c6a3e9aeb8b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459197"
---
# <a name="data-warehouse-units-dwus-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Data Warehouse Units (DWUs) para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Recomendações sobre a escolha do número ideal de unidades de armazém de dados (DWUs) para otimizar o preço e o desempenho, e como alterar o número de unidades.

## <a name="what-are-data-warehouse-units"></a>O que são Unidades de Armazém de Dados

Uma [piscina SQL dedicada (anteriormente SQL DW)](sql-data-warehouse-overview-what-is.md) representa uma coleção de recursos analíticos que estão sendo a provisionados. Os recursos analíticos são definidos como uma combinação de CPU, memória e IO.

Estes três recursos são agregados em unidades de escala de computação chamadas Data Warehouse Units (DWUs). Uma DWU é uma medida abstrata e normalizada do desempenho e recursos de computação.

Uma alteração ao seu nível de serviço altera o número de DWUs que estão disponíveis para o sistema, o que por sua vez ajusta o desempenho e o custo do seu sistema.

Para um desempenho mais elevado, pode aumentar o número de unidades de armazém de dados. Para menos desempenho, reduza as unidades de armazém de dados. Os custos de armazenamento e de computação são faturados em separado, pelo que a alteração das unidades do armazém de dados não afeta os custos de armazenamento.

O desempenho das unidades de armazém de dados baseia-se nestas métricas de carga de trabalho do armazém de dados:

- Quão rápida uma consulta padrão dedicada sql pool (anteriormente SQL DW) pode digitalizar um grande número de linhas e, em seguida, realizar uma agregação complexa. Esta operação é intensiva em I/O e CPU.
- A rapidez com que a piscina de SQL dedicada (anteriormente SQL DW) pode ingerir dados de Azure Storage Blobs ou Azure Data Lake. Esta operação é de rede e CPU intensivo.
- A rapidez com que o [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) comando T-SQL pode copiar uma mesa. Esta operação envolve a leitura de dados do armazenamento, distribuindo-os pelos nós do aparelho e escrevendo novamente para armazenamento. Esta operação é CPU, IO, e rede intensiva.

Aumento dos DWUs:

- Altera linearmente o desempenho do sistema para digitalizações, agregações e declarações ctas
- Aumenta o número de leitores e escritores para operações de carga polyBase
- Aumenta o número máximo de consultas simultâneas e slots de conuncy.

## <a name="service-level-objective"></a>Objetivo de Nível de Serviço

O Objetivo de Nível de Serviço (SLO) é a configuração de escalabilidade que determina o custo e o nível de desempenho do seu armazém de dados. Os níveis de serviço para a Gen2 são medidos em unidades de armazém de dados computacional (cDWU), por exemplo DW2000c. Os níveis de serviço da Gen1 são medidos em DWUs, por exemplo DW2000.

O Objetivo de Nível de Serviço (SLO) é a configuração de escalabilidade que determina o custo e o nível de desempenho da sua piscina DE SQL dedicada (anteriormente SQL DW). Os níveis de serviço para o pool SQL dedicado à Gen2 (anteriormente SQL DW) são medidos em unidades de armazém de dados (DWU), por exemplo DW2000c.

> [!NOTE]
> Pool SQL dedicado (anteriormente SQL DW) Gen2 adicionou recentemente capacidades de escala adicionais para suportar níveis de computação tão baixos como 100 cDWU. Os armazéns de dados existentes atualmente na Gen1 que requerem os níveis de computação mais baixos podem agora fazer upgrade para a Gen2 nas regiões que estão atualmente disponíveis sem custos adicionais.  Se a sua região ainda não for apoiada, ainda pode fazer upgrade para uma região apoiada. Para mais informações, consulte [Upgrade para a Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Em T-SQL, a definição de SERVICE_OBJETIVE determina o nível de serviço e o nível de desempenho da sua piscina SQL dedicada (anteriormente SQL DW).

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Níveis de desempenho e unidades de armazém de dados

Cada nível de desempenho utiliza uma unidade de medida ligeiramente diferente para as suas unidades de armazém de dados. Esta diferença reflete-se na fatura, uma vez que a unidade de escala se traduz diretamente na faturação.

- Os armazéns de dados da Gen1 são medidos em Unidades de Armazém de Dados (DWUs).
- Os armazéns de dados da Gen2 são medidos em unidades de armazém de dados computacional (cDWUs).

Tanto os DWUs como os cDWUs suportam a computação de escala para cima ou para baixo, e para o cálculo de pausas quando não precisa de usar o armazém de dados. Estas operações são todas a pedido. A Gen2 usa uma cache local baseada em disco nos nós computacional para melhorar o desempenho. Quando escala ou pausa o sistema, a cache é invalidada e, por isso, é necessário um período de aquecimento da cache antes de ser alcançado um desempenho ótimo.  

Cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma quota [da Unidade de Transação de Bases de Dados (DTU)](../../azure-sql/database/service-tiers-dtu.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) que permite um número específico de unidades de armazém de dados. Para mais informações, consulte os limites de capacidade de gestão da [carga de trabalho.](sql-data-warehouse-service-capacity-limits.md#workload-management)

## <a name="capacity-limits"></a>Limites de capacidade

Cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma quota [da Unidade de Transação de Bases de Dados (DTU)](../../sql-database/sql-database-what-is-a-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) que permite um número específico de unidades de armazém de dados. Para mais informações, consulte os limites de capacidade de gestão da [carga de trabalho.](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management)

## <a name="how-many-data-warehouse-units-do-i-need"></a>Quantas unidades de armazém de dados preciso

O número ideal de unidades de armazém de dados depende muito da sua carga de trabalho e da quantidade de dados que carregou no sistema.

Passos para encontrar o melhor DWU para a sua carga de trabalho:

1. Comece por selecionar um DWU menor.
2. Monitorize o desempenho da sua aplicação à medida que testa os dados no sistema, observando o número de DWUs selecionados em comparação com o desempenho que observa.
3. Identifique quaisquer requisitos adicionais para períodos periódicos de atividade máxima. As cargas de trabalho que apresentem picos e calhas significativos em atividade podem ter de ser escaladas com frequência.

O pool de SQL dedicado (anteriormente SQL DW) é um sistema de escala-out que pode autilizar grandes quantidades de cálculo e consultas quantidades consideráveis de dados.

Para ver as suas verdadeiras capacidades de dimensionamento, especialmente em DWUs maiores, recomendamos o escalonamento do conjunto de dados à medida que escala para garantir que tem dados suficientes para alimentar os CPUs. Para testes de escala, recomendamos a utilização de pelo menos 1 TB.

> [!NOTE]
>
> O desempenho da consulta só aumenta com mais paralelização se o trabalho puder ser dividido entre nós de computação. Se descobrir que o dimensionamento não está a alterar o seu desempenho, poderá ter de sintonizar o design da sua mesa e/ou as suas consultas. Para obter orientação de afinação de consultas, consulte [Gerir as consultas do utilizador](cheat-sheet.md).

## <a name="permissions"></a>Permissões

A alteração das unidades de armazém de dados requer as permissões descritas na [BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

As funções incorporadas do Azure, tais como SQL DB Contributor e SQL Server Contributor podem alterar as definições de DWU.

## <a name="view-current-dwu-settings"></a>Ver as definições de DWU atuais

Para ver a definição atual do DWU:

1. Abra o SQL Server Object Explorer em Estúdio Visual.
2. Ligue-se à base de dados principal associada ao servidor lógico SQL.
3. Selecione a partir da sys.database_service_objetives vista de gestão dinâmica. Segue-se um exemplo:

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

2. Em **Escala**, mova o deslizador para a esquerda ou para a direita para alterar a definição DWU.

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **sim** para confirmar ou **não** para cancelar.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para alterar os DWUs, utilize o [cmdlet PowerShell Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) O exemplo a seguir define o objetivo de nível de serviço para DW1000 para a base de dados MySQLDW que está hospedada no servidor MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Para mais informações, consulte [os cmdlets PowerShell para piscina SQL dedicada (anteriormente SQL DW)](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="t-sql"></a>T-SQL

Com o T-SQL pode visualizar os DWUsettings atuais, alterar as definições e verificar o progresso.

Para alterar os DWUs:

1. Ligue-se à base de dados principal associada ao seu servidor.
2. Utilize a declaração DE TSQL [ALTER DATABASE.](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) O exemplo a seguir define o objetivo do nível de serviço para DW1000c para a base de dados MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>APIs REST

Para alterar os DWUs, utilize a API [de Base de Dados de Criação ou Atualização.](/rest/api/sql/databases/createorupdate) O exemplo a seguir define o objetivo do nível de serviço para DW1000c para a base de dados MySQLDW, que está hospedada no servidor MyServer. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Para obter mais exemplos de API REST, consulte [ASFIT REST para piscina SQL dedicada (anteriormente SQL DW)](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="check-status-of-dwu-changes"></a>Verificar o estado das alterações da DWU

As alterações da DWU podem demorar vários minutos a ser concluídas. Se estiver a escalonar automaticamente, considere a lógica de implementação para garantir que determinadas operações foram concluídas antes de prosseguir com outra ação.

Verificar o estado da base de dados através de vários pontos finais permite-lhe implementar corretamente a automatização. O portal fornece notificação após a conclusão de uma operação e das bases de dados do estado atual, mas não permite a verificação programática do Estado.

Não é possível verificar o estado da base de dados para operações de escala com o portal Azure.

Para verificar o estado das alterações da DWU:

1. Ligue-se à base de dados principal associada ao seu servidor.
2. Envie a seguinte consulta para verificar o estado da base de dados.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Enviar a seguinte consulta para verificar o estado de funcionamento

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```

Este DMV devolve informações sobre várias operações de gestão na sua piscina de SQL dedicada (anteriormente SQL DW) como a operação e o estado da operação, que é IN_PROGRESS ou CONCLUÍDA.

## <a name="the-scaling-workflow"></a>O fluxo de trabalho de escala

Quando inicia uma operação em escala, o sistema primeiro elimina todas as sessões abertas, retirando quaisquer transações abertas para garantir um estado consistente. Para operações de escala, a escala só ocorre após a conclusão desta reversão transacional.  

- Para uma operação de escala, o sistema destaca todos os nós computacional, prevê os nós de computação adicionais e, em seguida, reata à camada de armazenamento.
- Para uma operação de escala para baixo, o sistema destaca todos os nós computacional e, em seguida, reacagre apenas os nós necessários para a camada de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a gestão do desempenho, consulte [as aulas de Recursos para gestão da carga de trabalho](resource-classes-for-workload-management.md) e memória e [limites de concordância.](memory-concurrency-limits.md)
