---
title: Unidades de data warehouse (DWUs, cDWUs) no Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações sobre como escolher o número ideal de unidades de data warehouse (DWUs, cDWUs) para otimizar o preço e o desempenho e como alterar o número de unidades.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: 282fab70e3b6d1fcf81814b2dd599259e2396fb3
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036042"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Unidades de data warehouse (DWUs) e unidades de data warehouse de computação (cDWUs)

Recomendações sobre como escolher o número ideal de unidades de data warehouse (DWUs, cDWUs) para otimizar o preço e o desempenho e como alterar o número de unidades.

## <a name="what-are-data-warehouse-units"></a>O que são unidades de data warehouse

O Azure SQL Data Warehouse CPU, memória e e/s são agrupados em unidades de escala de computação chamadas de unidades de data warehouse (DWUs). Um DWU representa uma medida abstrata e normalizada dos recursos de computação e do desempenho. Uma alteração no seu nível de serviço altera o número de DWUs que estão disponíveis para o sistema, o que, por sua vez, ajusta o desempenho e o custo do seu sistema.

Para um melhor desempenho, você pode aumentar o número de unidades de data warehouse. Para menos desempenho, reduza data warehouse unidades. Os custos de armazenamento e de computação são cobrados separadamente, portanto, alterar data warehouse unidades não afeta os custos de armazenamento.

O desempenho de unidades de data warehouse baseia-se nessas métricas de carga de trabalho data warehouse:

- A velocidade com que uma consulta de data warehouse padrão pode verificar um grande número de linhas e, em seguida, executar uma agregação complexa. Esta operação tem uso intensivo de e/s e CPU.
- Quão rápido a data warehouse pode ingerir dados de blobs de armazenamento do Azure ou Azure Data Lake. Essa operação é intensiva na rede e na CPU.
- Quão rápido o [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) comando T-SQL pode copiar uma tabela. Essa operação envolve a leitura de dados do armazenamento, a distribuição entre os nós do dispositivo e a gravação no armazenamento novamente. Esta operação é de CPU, e/s e rede intensiva.

Aumentando DWUs:

- Altera linearmente o desempenho do sistema para verificações, agregações e instruções CTAS
- Aumenta o número de leitores e gravadores para operações de carregamento do polybase
- Aumenta o número máximo de consultas simultâneas e slots de simultaneidade.

## <a name="service-level-objective"></a>Objetivo de nível de serviço

O objetivo de nível de serviço (SLO) é a configuração de escalabilidade que determina o nível de custo e desempenho do seu data warehouse. Os níveis de serviço para Gen2 são medidos em cDWU (unidades de data warehouse de computação), por exemplo, DW2000c. Os níveis de serviço Gen1 são medidos em DWUs, por exemplo, DW2000.
  > [!NOTE]
  > O Azure SQL Data Warehouse Gen2 recentemente adicionou recursos de escala adicionais para dar suporte a camadas de computação tão baixas que 100 cDWU. Os data warehouses existentes atualmente em Gen1 que exigem as camadas de computação inferiores agora podem ser atualizados para Gen2 nas regiões que estão atualmente disponíveis sem custo adicional.  Se sua região ainda não tiver suporte, você ainda poderá atualizar para uma região com suporte. Para obter mais informações, consulte [atualizar para Gen2](upgrade-to-latest-generation.md).

No T-SQL, a configuração SERVICE_OBJECTIVE determina o nível de serviço e a camada de desempenho para sua data warehouse.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Níveis de desempenho e unidades de data warehouse

Cada nível de desempenho usa uma unidade de medida um pouco diferente para suas unidades de data warehouse. Essa diferença é refletida na fatura à medida que a unidade de escala é convertida diretamente em cobrança.

- Os data warehouses do Gen1 são medidos em unidades de data warehouse (DWUs).
- Os data warehouses do Gen2 são medidos em cDWUs (unidades de data warehouse de computação).

O DWUs e o cDWUs dão suporte ao dimensionamento da computação para cima ou para baixo e pausar a computação quando você não precisa usar o data warehouse. Essas operações são todas sob demanda. O Gen2 usa um cache local baseado em disco nos nós de computação para melhorar o desempenho. Quando você dimensiona ou pausa o sistema, o cache é invalidado e, portanto, um período de aquecimento do cache é necessário antes que o desempenho ideal seja atingido.  

À medida que você aumenta as unidades de data warehouse, você está aumentando linearmente os recursos de computação. O Gen2 fornece o melhor desempenho de consulta e dimensionamento mais alto. Os sistemas Gen2 também fazem o máximo de uso do cache.

### <a name="capacity-limits"></a>Limites de capacidade

Cada SQL Server (por exemplo, myserver.database.windows.net) tem uma cota de [DTU (unidade de transação de banco de dados)](../sql-database/sql-database-what-is-a-dtu.md) que permite um número específico de unidades de data warehouse. Para obter mais informações, consulte os [limites de capacidade de gerenciamento de carga de trabalho](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Quantas unidades de data warehouse são necessárias

O número ideal de unidades de data warehouse depende muito da carga de trabalho e da quantidade de dados que você carregou no sistema.

Etapas para encontrar o melhor DWU para sua carga de trabalho:

1. Comece selecionando um DWU menor.
2. Monitore o desempenho do aplicativo durante o teste de cargas de dados no sistema, observando o número de DWUs selecionado em comparação com o desempenho que você observa.
3. Identifique quaisquer requisitos adicionais para períodos periódicos de atividade de pico. As cargas de trabalho que mostram picos significativos e ciclos na atividade podem precisar ser dimensionadas com frequência.

O SQL Data Warehouse é um sistema de escalabilidade horizontal que pode provisionar enormes quantidades de dados de computação e de considerável de consulta. Para ver seus recursos verdadeiros de dimensionamento, especialmente em DWUs maiores, é recomendável dimensionar o conjunto de dados conforme você dimensiona para garantir que você tenha dados suficientes para alimentar as CPUs. Para testes de escala, é recomendável usar pelo menos 1 TB.

> [!NOTE]
>
> O desempenho da consulta aumenta com mais paralelização se o trabalho puder ser dividido entre nós de computação. Se você achar que o dimensionamento não está alterando o desempenho, talvez seja necessário ajustar o design da tabela e/ou suas consultas. Para obter diretrizes de ajuste de consulta, consulte [gerenciar consultas de usuário](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Permissões

Alterar as unidades de data warehouse requer as permissões descritas em [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Funções internas para recursos do Azure, como colaborador de BD SQL e colaborador de SQL Server podem alterar as configurações de DWU.

## <a name="view-current-dwu-settings"></a>Exibir configurações de DWU atuais

Para exibir a configuração atual de DWU:

1. Abra Pesquisador de Objetos do SQL Server no Visual Studio.
2. Conecte-se ao banco de dados mestre associado ao servidor de banco de dados SQL lógico.
3. Selecione na exibição de gerenciamento dinâmico sys. database_service_objectives. Segue-se um exemplo:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Alterar unidades de data warehouse

### <a name="azure-portal"></a>Portal do Azure

Para alterar DWUs ou cDWUs:

1. Abra o [portal do Azure](https://portal.azure.com), abra o banco de dados e clique em **escala**.

2. Em **escala**, mova o controle deslizante para a esquerda ou para a direita para alterar a configuração DWU.

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **sim** para confirmar ou **não** para cancelar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para alterar o DWUs ou o cDWUs, use o cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) do PowerShell. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW que está hospedado no servidor meuservidor.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Para obter mais informações, consulte [cmdlets do PowerShell para SQL data warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Com o T-SQL, você pode exibir as configurações atuais de DWU ou cDWU, alterar as configurações e verificar o progresso.

Para alterar DWUs ou cDWUs:

1. Conecte-se ao banco de dados mestre associado ao seu servidor de banco de dados SQL lógico.
2. Use a instrução TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) . O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>APIs REST

Para alterar o DWUs, use a API REST [criar ou atualizar banco de dados](/rest/api/sql/databases/createorupdate) . O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que é hospedado no servidor meuservidor. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Para obter mais exemplos de API REST, consulte [APIs REST para SQL data warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Verificar o status das alterações de DWU

As alterações DWU podem levar vários minutos para serem concluídas. Se você estiver Dimensionando automaticamente, considere implementar a lógica para garantir que determinadas operações tenham sido concluídas antes de prosseguir com outra ação.

A verificação do estado do banco de dados por meio de vários pontos de extremidade permite que você implemente corretamente a automação. O portal fornece notificação após a conclusão de uma operação e o estado atual dos bancos de dados, mas não permite a verificação programática do estado.

Você não pode verificar o estado do banco de dados para operações de expansão com o portal do Azure.

Para verificar o status das alterações de DWU:

1. Conecte-se ao banco de dados mestre associado ao seu servidor de banco de dados SQL lógico.
2. Envie a consulta a seguir para verificar o estado do banco de dados.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Enviar a consulta a seguir para verificar o status da operação

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Essa DMV retorna informações sobre várias operações de gerenciamento em seu SQL Data Warehouse, como a operação e o estado da operação, que é IN_PROGRESS ou COMPLETED.

## <a name="the-scaling-workflow"></a>O fluxo de trabalho de dimensionamento

Quando você inicia uma operação de dimensionamento, o sistema primeiro interrompe todas as sessões abertas, revertendo as transações abertas para garantir um estado consistente. Para operações de escala, o dimensionamento ocorre apenas após a conclusão desta reversão transacional.  

- Para uma operação de expansão, o sistema desanexa todos os nós de computação, provisiona os nós de computação adicionais e, em seguida, anexa novamente à camada de armazenamento.
- Para uma operação de redução, o sistema desanexa todos os nós de computação e, em seguida, anexa apenas os nós necessários à camada de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como gerenciar o desempenho, consulte [classes de recurso para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) e [limites de memória e simultaneidade](memory-and-concurrency-limits.md).
