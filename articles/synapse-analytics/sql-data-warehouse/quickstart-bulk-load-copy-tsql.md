---
title: 'Quickstart: Dados de carga a granel usando uma única declaração T-SQL'
description: Dados de carga a granel utilizando a declaração COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83b5804888379316b855c36f803f646cec102d9e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024590"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Quickstart: Dados de carga a granel utilizando a declaração COPY

Neste arranque rápido, você irá amontoa dados de carga na sua piscina de SQL dedicada usando a [declaração](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) copy simples e flexível para ingestão de dados de alto rendimento. A declaração COPY é o utilitário de carregamento recomendado, pois permite-lhe carregar de forma perfeita e flexível os dados fornecendo a funcionalidade para:

- Permitir que utilizadores privilegiados mais baixos carreguem sem precisar de permissões rígidas do CONTROL no armazém de dados
- Aproveite apenas uma única declaração T-SQL sem ter que criar quaisquer objetos de base de dados adicionais
- Aproveite um modelo de permissão mais fino sem expor as chaves da conta de armazenamento utilizando assinaturas de acesso ao share (SAS)
- Especifique uma conta de armazenamento diferente para a localização ERRORFILE (REJECTED_ROW_LOCATION)
- Personalize os valores predefinidos para cada coluna-alvo e especifique campos de dados de origem para carregar em colunas-alvo específicas
- Especificar um exterminador de linha personalizado para ficheiros CSV
- Cadeia de fuga, deslimiters de campo e linha para ficheiros CSV
- Alavancar os formatos sql server date para ficheiros CSV
- Especificar wildcards e vários ficheiros no caminho da localização do armazenamento

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart assume que já tem uma piscina SQL dedicada. Se não tiver sido criada uma piscina SQL dedicada, utilize o arranque rápido do [portal Create and Connect](create-data-warehouse-portal.md) - portal.

## <a name="set-up-the-required-permissions"></a>Configurar as permissões necessárias

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Criar a tabela-alvo

Neste exemplo, vamos carregar dados do conjunto de dados de táxi de Nova Iorque. Carregaremos uma mesa chamada Trip que representa viagens de táxi realizadas num único ano. Executar o seguinte para criar a tabela:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Executar a declaração COPY

Execute a seguinte declaração COPY que carregará os dados da conta de armazenamento da bolha Azure na tabela Trip.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitorize a carga

Verifique se a sua carga está a progredir, executando periodicamente a seguinte consulta:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Próximos passos

- Para obter as melhores práticas de carregamento de dados, consulte [as Melhores Práticas para Carregar Dados.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)
- Para obter informações sobre como gerir os recursos para as suas cargas de dados, consulte [o Isolamento da Carga de Trabalho.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql) 
