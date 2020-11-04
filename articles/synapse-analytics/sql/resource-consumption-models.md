---
title: Consumo de recursos SQL de Sinapse
description: Saiba mais sobre os modelos de consumo de SQL da Synapse em Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 1a78142ded7be46bdc06c49d6e0a26ef8b266300
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318401"
---
# <a name="synapse-sql-resource-consumption"></a>Consumo de recursos SQL de Sinapse

Este artigo descreve os modelos de consumo de recursos do Synapse SQL (pré-visualização).

## <a name="serverless-sql-pool"></a>Piscina SQL sem servidor

A piscina SQL sem servidor é um serviço de pagamento por consulta que não requer que escolha o tamanho certo. O sistema ajusta-se automaticamente com base nos seus requisitos, libertando-o da gestão da sua infraestrutura e escolhendo o tamanho certo para a sua solução.

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Pool SQL dedicado - Unidades de Armazém de Dados (DWUs) e unidades de armazém de dados computacional (cDWUs)

Recomendações sobre a escolha do número ideal de unidades de armazém de dados (DWUs) para otimizar o preço e o desempenho, e como alterar o número de unidades.

### <a name="data-warehouse-units"></a>Unidades do Data Warehouse

Uma piscina Sinapse SQL representa uma coleção de recursos analíticos que estão sendo a provisionados. Os recursos analíticos são definidos como uma combinação de CPU, memória e IO. Estes três recursos são agregados em unidades de escala de computação chamadas Data Warehouse Units (DWUs). Uma DWU é uma medida abstrata e normalizada do desempenho e recursos de computação. Uma alteração ao seu nível de serviço altera o número de DWUs que estão disponíveis para o sistema. Por sua vez, esta alteração ajusta o desempenho e o custo do seu sistema.

Para um desempenho mais elevado, pode aumentar o número de unidades de armazém de dados. Para menos desempenho, reduza as unidades de armazém de dados. Os custos de armazenamento e de computação são faturados em separado, pelo que a alteração das unidades do armazém de dados não afeta os custos de armazenamento.

O desempenho das unidades de armazém de dados baseia-se nestas métricas de carga de trabalho do armazém de dados:

- A rapidez com que uma consulta padrão de armazenamento de dados pode digitalizar um grande número de linhas e, em seguida, realizar uma agregação complexa. Esta operação é intensiva em I/O e CPU.
- A rapidez com que o armazém de dados pode ingerir dados de Azure Storage Blobs ou Azure Data Lake. Esta operação é de rede e CPU intensivo.
- A rapidez com que o [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) comando T-SQL pode copiar uma mesa. Esta operação envolve a leitura de dados do armazenamento, distribuindo-os pelos nós do aparelho e escrevendo novamente para armazenamento. Esta operação é CPU, IO, e rede intensiva.

Aumento dos DWUs:

- Altera linearmente o desempenho do sistema para digitalizações, agregações e declarações ctas
- Aumenta o número de leitores e escritores para operações de carga polyBase
- Aumenta o número máximo de consultas simultâneas e slots de conuncy.

### <a name="service-level-objective"></a>Objetivo de Nível de Serviço

O Objetivo de Nível de Serviço (SLO) é a configuração de escalabilidade que determina o custo e o nível de desempenho do seu armazém de dados. Os níveis de serviço para a Gen2 são medidos em unidades de armazém de dados computacional (cDWU), por exemplo DW2000c. Os níveis de serviço da Gen1 são medidos em DWUs, por exemplo DW2000.

O Objetivo de Nível de Serviço (SLO) é a configuração de escalabilidade que determina o custo e o nível de desempenho do seu armazém de dados. Os níveis de serviço para o pool SQL dedicado à Gen2 são medidos em unidades de armazém de dados (DWU), por exemplo DW2000c.

> [!NOTE]
> A Azure Synapse Analytics Gen2 adicionou recentemente capacidades de escala adicionais para suportar níveis de computação tão baixos como 100 cDWU. Os armazéns de dados existentes atualmente na Gen1 que requerem os níveis de computação mais baixos podem agora fazer upgrade para a Gen2 nas regiões que estão atualmente disponíveis sem custos adicionais.  Se a sua região ainda não for apoiada, ainda pode fazer upgrade para uma região apoiada. Para mais informações, consulte [Upgrade para a Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Em T-SQL, a definição de SERVICE_OBJETIVE determina o nível de serviço e o nível de desempenho da sua piscina SQL dedicada.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Níveis de desempenho e unidades de armazém de dados

Cada nível de desempenho utiliza uma unidade de medida ligeiramente diferente para as suas unidades de armazém de dados. Esta diferença reflete-se na fatura, uma vez que a unidade de escala se traduz diretamente na faturação.

- Os armazéns de dados da Gen1 são medidos em Unidades de Armazém de Dados (DWUs).
- Os armazéns de dados da Gen2 são medidos em unidades de armazém de dados computacional (cDWUs).

Tanto os DWUs como os cDWUs suportam a computação de escala para cima ou para baixo, e para o cálculo de pausas quando não precisa de usar o armazém de dados. Estas operações são todas a pedido. A Gen2 usa uma cache local baseada em disco nos nós computacional para melhorar o desempenho. Quando escala ou pausa o sistema, a cache é invalidada e, por isso, é necessário um período de aquecimento da cache antes de ser alcançado um desempenho ótimo.  

À medida que aumentas as unidades de armazém de dados, estás a aumentar linearmente os recursos informáticos. A Gen2 proporciona o melhor desempenho de consulta e a mais alta escala. Os sistemas gen2 também fazem o maior uso da cache.

#### <a name="capacity-limits"></a>Limites de capacidade

Cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma quota [da Unidade de Transação de Bases de Dados (DTU)](../../azure-sql/database/service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) que permite um número específico de unidades de armazém de dados. Para mais informações, consulte os limites de capacidade de gestão da [carga de trabalho.](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management)

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>Avalie o número de unidades de armazém de dados que precisa

O número ideal de unidades de armazém de dados depende muito da sua carga de trabalho e da quantidade de dados que carregou no sistema.

Passos para encontrar o melhor DWU para a sua carga de trabalho:

1. Comece por selecionar um DWU menor.
2. Monitorize o desempenho da sua aplicação à medida que testa os dados no sistema, observando o número de DWUs selecionados em comparação com o desempenho que observa.
3. Identifique quaisquer requisitos adicionais para períodos periódicos de atividade máxima. As cargas de trabalho que apresentem picos e calhas significativos em atividade podem ter de ser escaladas com frequência.

O pool SQL é um sistema de escala-out que pode abasfar grandes quantidades de quantidades de dados computacional e de consulta. Para ver as suas verdadeiras capacidades de dimensionamento, especialmente em DWUs maiores, recomendamos o escalonamento do conjunto de dados à medida que escala para garantir que tem dados suficientes para alimentar os CPUs. Para testes de escala, recomendamos a utilização de pelo menos 1 TB.

> [!NOTE]
>
> O desempenho da consulta só aumenta com mais paralelização se o trabalho puder ser dividido entre nós de computação. Se descobrir que o dimensionamento não está a alterar o seu desempenho, poderá ter de sintonizar o design da sua mesa e/ou as suas consultas. Para obter orientação de afinação de consultas, consulte [Gerir as consultas do utilizador](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="permissions"></a>Permissões

A alteração das unidades de armazém de dados requer as permissões descritas na [BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

As funções incorporadas do Azure, tais como SQL DB Contributor e SQL Server Contributor podem alterar as definições de DWU.

#### <a name="view-current-dwu-settings"></a>Ver as definições de DWU atuais

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

### <a name="change-data-warehouse-units"></a>Alterar unidades de armazém de dados

#### <a name="azure-portal"></a>Portal do Azure

Para alterar DWUs:

1. Abra o [portal Azure,](https://portal.azure.com)abra a sua base de dados e selecione **Escala**.

2. Em **Escala** , mova o deslizador para a esquerda ou para a direita para alterar a definição DWU.

3. Selecione **Guardar**. É apresentada uma mensagem de confirmação. Selecione **sim** para confirmar ou **não** para cancelar.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para alterar os DWUs, utilize o [cmdlet PowerShell Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) O exemplo a seguir define o objetivo de nível de serviço para DW1000 para a base de dados MySQLDW que está hospedada no servidor MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Para mais informações, consulte [os cmdlets PowerShell para Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

Com o T-SQL pode visualizar os DWUsettings atuais, alterar as definições e verificar o progresso.

Para alterar os DWUs:

1. Ligue-se à base de dados principal associada ao seu servidor.
2. Utilize a declaração DE TSQL [ALTER DATABASE.](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) O exemplo a seguir define o objetivo do nível de serviço para DW1000c para a base de dados MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>APIs REST

Para alterar os DWUs, utilize a API [de Base de Dados de Criação ou Atualização.](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) O exemplo a seguir define o objetivo do nível de serviço para DW1000c para a base de dados MySQLDW, que está hospedada no servidor MyServer. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Para obter mais exemplos de API REST, consulte [REST APIs para Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Verificar o estado das alterações da DWU

As alterações da DWU podem demorar vários minutos a ser concluídas. Se estiver a escalonar automaticamente, considere implementar a lógica para garantir que determinadas operações foram concluídas antes de prosseguir com outra ação.

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

Este DMV devolve informações sobre várias operações de gestão no seu pool de SQL dedicado, como a operação e o estado da operação, que é IN_PROGRESS ou concluído.

### <a name="the-scaling-workflow"></a>O fluxo de trabalho de escala

Quando inicia uma operação em escala, o sistema primeiro elimina todas as sessões abertas, retirando quaisquer transações abertas para garantir um estado consistente. Para operações de escala, a escala só ocorre após a conclusão desta reversão transacional.  

- Para uma operação de escala, o sistema destaca todos os nós computacional, prevê os nós de computação adicionais e, em seguida, reata à camada de armazenamento.
- Para uma operação de escala para baixo, o sistema destaca todos os nós computacional e, em seguida, reacagre apenas os nós necessários para a camada de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a gestão do desempenho, consulte [as aulas de Recursos para gestão da carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e memória e [limites de concordância.](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
