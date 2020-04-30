---
title: 'Quickstart: Dados de carga a granel utilizando uma única declaração T-SQL'
description: Dados de carga a granel utilizando a declaração COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d39b3085a802ca0ff745ab1f63f4a8fba966ea48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81115009"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Arranque rápido: Dados de carga a granel utilizando a declaração COPY

Neste arranque rápido, irá em massa carregar dados para o seu pool SQL utilizando a simples e flexível [declaração copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) para ingestão de dados de alta produção. A declaração COPY é o utilitário de carregamento recomendado, pois permite-lhe carregar de forma perfeita e flexível os dados, fornecendo funcionalidade para:

- Permitir que utilizadores mais desfavorecidos carreguem sem precisar de permissões rigorosas de CONTROLO no armazém de dados
- Alavancar apenas uma única declaração de T-SQL sem ter que criar quaisquer objetos de base de dados adicionais
- Aproveite um modelo de permissão mais fino sem expor chaves de conta de armazenamento utilizando assinaturas de acesso à partilha (SAS)
- Especifique uma conta de armazenamento diferente para a localização ERRORFILE (REJECTED_ROW_LOCATION)
- Personalize os valores predefinidos para cada coluna-alvo e especifique os campos de dados de origem para carregar em colunas específicas de alvo
- Especifique um exterminador de linha personalizado para ficheiros CSV
- Delimitadores de cadeias de fuga, campo e linha para ficheiros CSV
- Aproveite os formatos de data do servidor SQL para ficheiros CSV
- Especifique wildcards e vários ficheiros no caminho do local de armazenamento

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart assume que já tem uma piscina SQL. Se não tiver sido criada uma piscina SQL, utilize o [Create and Connect - portal](create-data-warehouse-portal.md) quickstart.

## <a name="create-the-target-table"></a>Criar a tabela-alvo

Neste exemplo, vamos carregar dados do conjunto de dados de táxi sinuoso de Nova Iorque. Carregaremos uma mesa chamada Trip que representa viagens de táxi feitas dentro de um ano. Executar o seguinte para criar a tabela:

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

## <a name="run-the-copy-statement"></a>Executar a declaração copy

Faça a seguinte declaração de COPY que irá carregar dados da conta de armazenamento de blob Azure na tabela Trip.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitorize a carga

Verifique se a sua carga está a progredir executando periodicamente a seguinte consulta:

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

## <a name="next-steps"></a>Passos seguintes

- Para obter as melhores práticas de carregamento de dados, consulte [as Melhores Práticas para carregar dados.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)
- Para obter informações sobre como gerir os recursos para as suas cargas de dados, consulte [O Isolamento da Carga de Trabalho](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
