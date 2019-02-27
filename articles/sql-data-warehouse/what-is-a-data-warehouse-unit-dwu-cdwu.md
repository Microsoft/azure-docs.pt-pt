---
title: Unidades do Data Warehouse (DWUs, cDWUs) no Azure SQL Data Warehouse | Documentos da Microsoft
description: Recomendações sobre como escolher o número ideal de unidades do data warehouse (DWUs, cDWUs) para otimizar o preço e desempenho e como alterar o número de unidades.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7da813022527830a5ede62c9f4ce98645fe63d4b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871610"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Unidades do Data Warehouse (DWUs) e de computação (cDWUs) de unidades do Data Warehouse
Recomendações sobre como escolher o número ideal de unidades do data warehouse (DWUs, cDWUs) para otimizar o preço e desempenho e como alterar o número de unidades. 

## <a name="what-are-data-warehouse-units"></a>O que são unidades de armazém de dados?
Com o SQL Data Warehouse CPU, memória e e/s são agrupados em unidades de escala de computação chamado unidades do Data Warehouse (DWUs). A DWU representa uma medida de abstrata, normalizada de recursos de computação e desempenho. Ao alterar o nível de serviço é alterar o número de DWUs que são alocados para o sistema, que por sua vez ajusta o desempenho e o custo, de seu sistema. 

Para pagar um desempenho mais elevado, pode aumentar o número de unidades de armazém de dados. Para pagar o menor desempenho, reduza as unidades de armazém de dados. Os custos de armazenamento e computação são faturados separadamente, para que as unidades de armazém de dados de alteração não afetam os custos de armazenamento.

Desempenho de unidades do data warehouse baseia-se estas métricas de carga de trabalho do armazém de dados:

- A rapidez pode que uma consulta de armazenamento de dados padrão é analisar um grande número de linhas e, em seguida, efetuar uma agregação complexa? Esta operação é intensivas de e/s e CPU.
- A rapidez o armazém de dados pode ingerir dados de Blobs de armazenamento do Azure ou do Azure Data Lake? Esta operação é a rede e intensivas da CPU. 
- A rapidez pode a [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) comando T-SQL copiar uma tabela? Esta operação envolve a leitura de dados do armazenamento, a distribuição dos mesmos por nós da aplicação e a escrita novamente no armazenamento. Esta operação é a CPU, IO e rede intensiva.

Aumentar as DWUs:
- Muda linearmente o desempenho do sistema para análises, agregações e instruções de CTAS
- Aumenta o número de leitores e gravadores para operações de carregamento de PolyBase
- Aumenta o número máximo de consultas em simultâneo e blocos de simultaneidade.

## <a name="service-level-objective"></a>Objetivo de nível de serviço
O objetivo de nível de serviço (SLO) é a definição de escalabilidade que determina o nível de desempenho e custo do seu armazém de dados. Os níveis de serviço de geração 2 são medidos em computação unidades do data warehouse (cDWU), por exemplo DW2000c. Níveis de serviço de geração 1 são medidos em DWUs, por exemplo, DW2000.
  > [!NOTE]
  > O Azure SQL Data Warehouse Gen2 recentemente adicionado capacidades de dimensionamento adicional para suportar escalões de computação como tão baixo como 100 cDWU. Armazéns de dados existentes no momento Gen1 que requerem escalões de computação de menor agora podem atualizar para ger2 nas regiões que estão atualmente disponíveis sem qualquer custo adicional.  Se a sua região ainda não é suportada, ainda pode atualizar para uma região suportada. Para obter mais informações, consulte [atualizar para ger2](upgrade-to-latest-generation.md).

No T-SQL a definição de SERVICE_OBJECTIVE determina o nível de serviço e o escalão de desempenho para o seu armazém de dados.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Escalões de desempenho e unidades de armazém de dados

Cada escalão de desempenho utiliza uma unidade ligeiramente diferente de medida para suas unidades de armazém de dados. Essa diferença é refletida da nota fiscal, como a unidade de escala traduz-se diretamente à faturação.

- Armazéns de dados de geração 1 são medidos em unidades do Data Warehouse (DWUs).
- Dados de geração 2 armazéns são medidos em unidades do Data Warehouse (cDWUs) de computação. 

As DWUs e cDWUs suportam computação de dimensionamento ou reduzir verticalmente e colocar em pausa a computação quando não precisar de utilizar o armazém de dados. Essas operações são tudo sob demanda. Geração 2 utiliza um cache em disco local em nós de computação para melhorar o desempenho. Ao dimensionar ou colocar em pausa o sistema, o cache é invalidado e por isso, um período de cache warming é necessário antes do desempenho ideal é alcançado.  

À medida que aumenta a unidades de armazém de dados, linearmente estão a aumentar a recursos de computação. Geração 2 fornece o melhor desempenho das consultas e a escala mais elevada. Estes sistemas de tirar o máximo partido da cache.

### <a name="capacity-limits"></a>Limites de capacidade
Cada SQL server (por exemplo, myserver.database.windows.net) tem um [unidades de transação de base de dados (DTU)](../sql-database/sql-database-what-is-a-dtu.md) quota que permite que um determinado número de unidades do data warehouse. Para obter mais informações, consulte a [limites de capacidade de gerenciamento de carga de trabalho](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Quantas unidades de armazém de dados é necessário?
O número ideal de unidades de armazém de dados depende muito sua carga de trabalho e a quantidade de dados que tenha carregado no sistema.

Passos para encontrar a melhor DWU para a sua carga de trabalho:

1. Comece por selecionar uma DWU menor. 
2. Monitorize o desempenho da aplicação durante o teste cargas de dados no sistema, observar o número de DWUs selecionado em comparação comparado o desempenho que observar.
3. Identifique quaisquer requisitos adicionais para periódicos períodos de atividade de pico. Se a carga de trabalho mostra picos significativos e os vales na atividade e há um bom motivo para dimensionar com frequência.

O SQL Data Warehouse é um sistema de escalamento horizontal que pode aprovisionar grandes quantidades de computação e de consulta quantidades dimensionável de dados. Para ver as suas verdadeiras capacidades de dimensionamento, especialmente no maior número de DWUs, recomendamos que aumentando o conjunto de dados, à medida que aumenta para se certificar de que tem dados suficientes para alimentar as CPUs. Para testes de dimensionamento, recomendamos que utilize, pelo menos, 1 TB.

> [!NOTE]
>
> Desempenho de consultas só aumenta com da paralelização de mais, se o trabalho pode ser dividido entre nós de computação. Se achar que dimensionamento é não alterar o desempenho, se pretender otimizar seu design de tabela e/ou as suas consultas. Para o guia de sintonização de consulta, consulte [gerir consultas de utilizador](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Permissões

Alterar as unidades de armazém de dados exige as permissões descritas [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

Funções incorporadas para recursos do Azure, tais como contribuinte da BD SQL e contribuinte do SQL Server podem alterar as definições de DWU. 

## <a name="view-current-dwu-settings"></a>Ver definições de DWU atuais

Para ver a definição de DWU atual:

1. Abra o SQL Server Object Explorer no Visual Studio.
2. Ligar à base de dados mestra associado ao servidor lógico de base de dados SQL.
3. Selecione a vista de gestão dinâmica sys.database_service_objectives. Segue-se um exemplo: 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Alterar as unidades de armazém de dados

### <a name="azure-portal"></a>Portal do Azure
Para alterar o número de DWUs ou cDWUs:

1. Abra o [portal do Azure](https://portal.azure.com), abra a sua base de dados e clique em **dimensionamento**.

2. Sob **dimensionamento**, mova o controlo de deslize à esquerda ou o botão direito do rato para alterar a definição de DWU.

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **sim** para confirmar ou **não** para cancelar.

### <a name="powershell"></a>PowerShell
Para alterar o número de DWUs ou cDWUs, utilize o [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) cmdlet do PowerShell. O exemplo seguinte define o objetivo de nível de serviço para DW1000 MySQLDW que está alojado no servidor MyServer da base de dados.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Para obter mais informações, consulte [cmdlets do PowerShell para o SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
Com o T-SQL pode ver as definições de DWU ou cDWU atuais, alterar as definições e verificar o progresso. 

Para alterar o número de DWUs ou cDWUs:

1. Ligar à base de dados mestra associado com o servidor de base de dados SQL lógico.
2. Utilize o [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) instrução TSQL. O exemplo seguinte define o objetivo de nível de serviço para DW1000 MySQLDW da base de dados. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>APIs REST

Para alterar as DWUs, utilize o [criar ou atualizar banco de dados](/rest/api/sql/databases/createorupdate) REST API. O exemplo seguinte define o objetivo de nível de serviço para DW1000 MySQLDW que está alojado no servidor MyServer da base de dados. O servidor está num grupo de recursos do Azure com o nome ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Para obter mais exemplos de REST API, consulte [APIs de REST para o SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Verificar o estado das alterações DWU

Alterações DWU podem demorar vários minutos a concluir. Se estão a dimensionar automaticamente, pondere implementar lógica para garantir que determinadas operações concluídas antes de continuar com outra ação. 

A verificar o estado da base de dados por meio de vários pontos de extremidade permite-lhe implementar corretamente a automatização. O portal fornece notificação após a conclusão de uma operação e o estado atual de bases de dados, mas não permite programático a verificação do Estado. 

Não é possível verificar o estado da base de dados para operações de escalamento horizontal com o portal do Azure.

Para verificar o estado das alterações DWU:

1. Ligar à base de dados mestra associado com o servidor de base de dados SQL lógico.
2. Submeta a consulta seguinte para verificar o estado da base de dados.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Submeter a consulta seguinte para verificar o estado da operação

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Essa DMV devolve informações sobre a gestão de várias operações no seu armazém de dados SQL como a operação e o estado da operação, que está ser IN_PROGRESS ou COMPLETED.

## <a name="the-scaling-workflow"></a>O fluxo de trabalho de dimensionamento

Quando inicia uma operação de dimensionamento, o sistema interrompe primeiro sessões abertos, a reverter quaisquer transações abertas para garantir um estado consistente. Para operações de dimensionamento, dimensionamento apenas ocorre após a conclusão deste reversão transacional.  

- Para uma operação de aumento vertical, as disposições de sistema adicionais de computação e, em seguida, volta a ligar para a camada de armazenamento. 
- Para uma operação de redução vertical, desnecessária nós desanexar a partir do armazenamento e voltar a ligá para os nós restantes.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a gestão de desempenho, consulte [classes de recursos para a gestão da carga de trabalho](resource-classes-for-workload-management.md) e [limites de memória e simultaneidade](memory-and-concurrency-limits.md).



