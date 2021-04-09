---
title: O recurso DTU limita as bases de dados individuais
description: Esta página descreve alguns limites de recursos DTU comuns para bases de dados individuais na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: c530d584282cebba78c095798944e48d7efe2c66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625658"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Limites de recursos para bases de dados únicas utilizando o modelo de compra DTU - Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para bases de dados únicas da Azure SQL Database utilizando o modelo de compra DTU.

Para a aquisição de limites de recursos para piscinas elásticas, consulte [os limites de recursos da DTU - piscinas elásticas.](resource-limits-dtu-elastic-pools.md) Para limites de recursos vCore, consulte [os limites de recursos vCore - bases de dados únicas](resource-limits-vcore-single-databases.md) e [limites de recursos vCore - piscinas elásticas](resource-limits-vcore-elastic-pools.md). Para obter mais informações sobre os diferentes modelos de compra, consulte [modelos de compras e tiers de serviço.](purchasing-models.md)

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Base de dados única: Tamanhos de armazenamento e tamanhos de cálculo

As tabelas a seguir mostram os recursos disponíveis para uma única base de dados em cada nível de serviço e tamanho de cálculo. Pode definir o nível de serviço, o tamanho do cálculo e o valor de armazenamento para uma única base de dados utilizando o [portal Azure](single-database-manage.md#the-azure-portal), [Transact-SQL,](single-database-manage.md#transact-sql-t-sql) [PowerShell,](single-database-manage.md#powershell) [O CLI Azure,](single-database-manage.md#the-azure-cli)ou o [REST API](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Para obter orientações e considerações de escala, consulte [Escala uma única base de dados](single-database-scale.md)

### <a name="basic-service-tier"></a>Escalão de serviços básicos

| **Tamanho do cálculo** | **Básica** |
| :--- | --: |
| Máximo de DTUs | 5 |
| Armazenamento incluído (GB) | 2 |
| Armazenamento máximo (GB) | 2 |
| Armazenamento em OLTP na memória máx. (GB) |N/D |
| Trabalhadores max simultâneos (pedidos) | 30 |
| Sessões simultâneas máx. | 300 |
|||

> [!IMPORTANT]
> O nível de serviço Básico fornece menos de um vCore (CPU).  Para cargas de trabalho intensivas de CPU, recomenda-se um nível de serviço de S3 ou superior.
>
>No que diz respeito ao armazenamento de dados, o nível de serviço Básico é colocado em Blobs de Página Padrão. As Blobs Standard Page utilizam suportes de armazenamento baseados em discos rígidos (HDD) e são mais adequados para desenvolvimento, testes e outras cargas de trabalho pouco frequentes que são menos sensíveis à variabilidade do desempenho.
>

### <a name="standard-service-tier"></a>Escalão de serviço Standard

| **Tamanho do cálculo** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Máximo de DTUs | 10 | 20 | 50 | 100 |
| Armazenamento incluído (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| Armazenamento máximo (GB) | 250 | 250 | 250 | 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/D | N/D | N/D | N/D |
| Trabalhadores max simultâneos (pedidos)| 60 | 90 | 120 | 200 |
| Sessões simultâneas máx. |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> Consulte [as opções de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter detalhes sobre o custo adicional incorrido devido a qualquer armazenamento extra a provisionado.

> [!IMPORTANT]
> Os níveis Standard S0, S1 e S2 fornecem menos de um vCore (CPU).  Para cargas de trabalho intensivas de CPU, recomenda-se um nível de serviço de S3 ou superior.
>
>No que diz respeito ao armazenamento de dados, os níveis de serviço Standard S0 e S1 são colocados em Blobs de Página Standard. As Blobs Standard Page utilizam suportes de armazenamento baseados em discos rígidos (HDD) e são mais adequados para desenvolvimento, testes e outras cargas de trabalho pouco frequentes que são menos sensíveis à variabilidade do desempenho.
>

### <a name="standard-service-tier-continued"></a>Nível de serviço standard (continuado)

| **Tamanho do cálculo** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Máximo de DTUs | 200 | 400 | 800 | 1600 | 3.000 |
| Armazenamento incluído (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| Armazenamento máximo (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/D | N/D | N/D | N/D |N/D |
| Trabalhadores max simultâneos (pedidos)| 400 | 800 | 1600 | 3200 |6000 |
| Sessões simultâneas máx. |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> Consulte [as opções de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter detalhes sobre o custo adicional incorrido devido a qualquer armazenamento extra a provisionado.

### <a name="premium-service-tier"></a>Escalão de serviços Premium

| **Tamanho do cálculo** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Armazenamento incluído (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Armazenamento máximo (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Armazenamento em OLTP na memória máx. (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Trabalhadores max simultâneos (pedidos)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Sessões simultâneas máx. | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> Consulte [as opções de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/single/) para obter detalhes sobre o custo adicional incorrido devido a qualquer armazenamento extra a provisionado.

<sup>2</sup> De 1024 GB até 4096 GB em incrementos de 256 GB.

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China Norte, Alemanha Central e Alemanha Nordeste. Nestas regiões, o máximo de armazenamento no nível Premium é limitado a 1 TB.  Para obter mais informações, consulte [as limitações atuais do P11-P15.](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)
> [!NOTE]
> Para `tempdb` limites, consulte [os limites temporários](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Passos seguintes

- Para limites de recursos vCore para uma única base de [dados, consulte os limites de recursos para bases de dados únicas utilizando o modelo de compra vCore](resource-limits-vcore-single-databases.md)
- Para limites de recursos vCore para piscinas elásticas, consulte [os limites de recursos para piscinas elásticas utilizando o modelo de compra vCore](resource-limits-vcore-elastic-pools.md)
- Para limites de recursos DTU para piscinas elásticas, consulte [limites de recursos para piscinas elásticas utilizando o modelo de compra DTU](resource-limits-dtu-elastic-pools.md)
- Para limites de recursos para casos geridos em Instância Gerida Azure SQL, consulte [os limites de recursos de instância gerida SQL](../managed-instance/resource-limits.md).
- Para obter informações sobre os limites gerais do Azure, consulte [os limites de subscrição e serviços, quotas e restrições da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Para obter informações sobre os limites de recursos num servidor lógico do SQL, consulte [a visão geral dos limites de recursos num servidor lógico SQL](resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e subscrição.