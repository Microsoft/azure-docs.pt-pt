---
title: O recurso de DTU limita bancos de dados individuais
description: Esta página descreve alguns limites comuns de recursos de DTU para bancos de dados individuais no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 75611dd36196c722254d694e9753deb982e2570d
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539325"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra de DTU

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do banco de dados SQL do Azure usando o modelo de compra DTU.

Para os limites de recursos do modelo de compra de DTU para pools elásticos, consulte [limites de recursos de DTU – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md) Para limites de recursos vCore, consulte [limites de recursos VCORE-bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos VCORE-pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para obter mais informações sobre os diferentes modelos de compra, consulte [comprando modelos e camadas de serviço](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Banco de dados individual: tamanhos de armazenamento e tamanhos de computação

As tabelas a seguir mostram os recursos disponíveis para um único banco de dados em cada camada de serviço e tamanho de computação. Você pode definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento para um único banco de dados usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), o [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um banco de dados individual](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Escalão de serviços básicos

| **Tamanho da computação** | **Básica** |
| :--- | --: |
| Máximo de DTUs | 5 |
| Armazenamento incluído (GB) | 2 |
| Opções de armazenamento máximo (GB) | 2 |
| Armazenamento em OLTP na memória máx. (GB) |N/D |
| Máximo de trabalhos simultâneos (solicitações) | 30 |
| Sessões simultâneas máx. | 300 |
|||

> [!IMPORTANT]
> A camada de serviço básica fornece menos de um vCore (CPU).  Para cargas de trabalho com uso intensivo de CPU, é recomendável uma camada de serviço S3 ou superior. 
>
>Em relação ao armazenamento de dados, a camada de serviço básica é colocada em blobs de páginas padrão. Os blobs de páginas padrão usam mídia de armazenamento baseada em HDD (unidade de disco rígido) e são mais adequados para desenvolvimento, teste e outras cargas de trabalho acessadas com pouca frequência que são menos sensíveis à variabilidade de desempenho.
>

### <a name="standard-service-tier"></a>Escalão de serviço Standard

| **Tamanho da computação** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Máximo de DTUs | 10 | 20 | 50 | 100 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/D | N/D | N/D | N/D |
| Máximo de trabalhos simultâneos (solicitações)| 60 | 90 | 120 | 200 |
| Sessões simultâneas máx. |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> As camadas padrão S0, S1 e S2 fornecem menos de um vCore (CPU).  Para cargas de trabalho com uso intensivo de CPU, é recomendável uma camada de serviço S3 ou superior. 
>
>Em relação ao armazenamento de dados, as camadas de serviço S0 e S1 padrão são colocadas em blobs de páginas padrão. Os blobs de páginas padrão usam mídia de armazenamento baseada em HDD (unidade de disco rígido) e são mais adequados para desenvolvimento, teste e outras cargas de trabalho acessadas com pouca frequência que são menos sensíveis à variabilidade de desempenho.
>

### <a name="standard-service-tier-continued"></a>Camada de serviço Standard (continuação)

| **Tamanho da computação** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Máximo de DTUs | 200 | 400 | 800 | 1600 | 3000 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/D | N/D | N/D | N/D |N/D |
| Máximo de trabalhos simultâneos (solicitações)| 400 | 800 | 1600 | 3200 |6000 |
| Sessões simultâneas máx. |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Escalão de serviços Premium

| **Tamanho da computação** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Armazenamento incluído (GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| Opções de armazenamento máximo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| Armazenamento em OLTP na memória máx. (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Máximo de trabalhos simultâneos (solicitações)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Sessões simultâneas máx. | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* de 1024 GB até 4096 GB em incrementos de 256 GB

> [!IMPORTANT]
> Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto: Leste da China, Norte da China, Alemanha central, Alemanha nordeste, Oeste EUA Central, regiões de US DoD e central do governo dos EUA. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB.  Para obter mais informações, consulte [limitações atuais do P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Para limites de `tempdb`, consulte [limites de tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Passos seguintes

- Para limites de recursos vCore para um único banco de dados, confira [limites de recursos para bancos de dados individuais usando o modelo de compra vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para limites de recursos vCore para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para os limites de recursos de DTU para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra de DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recurso para instâncias gerenciadas, consulte [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor de banco de dados, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
