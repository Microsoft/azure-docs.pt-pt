---
title: Bases de Dados do Governo Azure Microsoft Docs
description: Isto fornece uma comparação de características e orientações sobre o desenvolvimento de candidaturas para o Governo Azure
services: azure-government
cloud: gov
documentationcenter: ''
author: zakramer
manager: liki
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 03/06/2019
ms.author: zakramer
ms.openlocfilehash: cbf3d59bafa5b11be0fa037487dc64fdb9075619
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355866"
---
# <a name="azure-government-databases"></a>Bases de dados do Governo Azure
## <a name="sql-database"></a>Base de Dados SQL
Para obter mais informações, consulte o Microsoft Security Center para o Motor de Base de<a href="https://msdn.microsoft.com/library/bb510589.aspx"> Dados SQL</a> e documentação de base de [dados Azure SQL](../sql-database/index.yml) para obter orientações adicionais sobre a configuração da visibilidade dos metadados e as melhores práticas de proteção.

### <a name="variations"></a>Variações
A Base de Dados SQL V12 está geralmente disponível no Governo Azure.

O endereço para servidores SQL Azure no Governo Azure é diferente:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
| Base de Dados SQL |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para o Azure SQL:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e processados no Microsoft Azure SQL podem conter dados regulados pelo Governo do Azure. Utilize ferramentas de base de dados para a transferência de dados de dados regulados pelo Governo do Azure. |Os metadados Azure SQL não estão autorizados a conter dados controlados pela exportação. Estes metadados incluem todos os dados de configuração introduzidos na criação e manutenção do seu produto de armazenamento.  Não introduza dados regulados/controlados nos seguintes campos: Nome da base de dados, nome de subscrição, grupos de recursos, nome do servidor, login de administração do Servidor, nomes de implementação, nomes de recursos, etiquetas de recursos |

## <a name="sql-data-warehouse"></a>SQL Data Warehouse
Para mais informações sobre este serviço e como usá-lo, consulte a documentação do Armazém de [Dados Azure SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

## <a name="sql-server-stretch-database"></a>SQL Server Stretch Database
Para mais detalhes sobre este serviço e como usá-lo, consulte a documentação da Base de Dados de Extensão do [Servidor Azure SQL](../sql-server-stretch-database/index.md)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
Para mais detalhes sobre este serviço e como usá-lo, consulte a [documentação do Azure Cosmos DB.](../cosmos-db/index.yml)

### <a name="variations"></a>Variações
O Azure Cosmos DB está geralmente disponível no Governo Azure com paridade para a versão pública. Uma exceção é a função **Add Azure Cognitive Search** atualmente, que não está disponível no Cosmos DB para o Governo Azure.

Além disso, os URLs para aceder ao Cosmos DB no Governo Azure são diferentes:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
| BD do Cosmos | *.documents.azure.com | *.documents.azure.us |


### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para o Azure Cosmos DB:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e processados no Azure Cosmos DB podem conter dados regulados pelo Governo de Azure. |Os metadados da Azure Cosmos DB não estão autorizados a conter dados controlados pela exportação. Não introduza dados regulados/controlados nos seguintes campos: **nome DB, nome de subscrição, grupos de recursos, etiquetas**de recursos . |


## <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
Para mais detalhes sobre este serviço e como usá-lo, consulte [Azure Cache para obter documentação redis](../azure-cache-for-redis/index.yml).

### <a name="variations"></a>Variações
Os URLs para aceder e gerir o Azure Cache para redis no Governo Azure são diferentes:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
| Ponto final de cache |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |

> [!NOTE]
> Todos os scripts e códigos devem ter em conta os pontos finais e ambientes apropriados. Para mais informações, consulte [Como se conectar a outras nuvens](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).
>
>

### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para o Azure Cache for Redis:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e processados em Azure Cache para Redis podem conter dados regulados pelo Governo de Azure. |Os metadados Azure Cache para os metadados Redis não estão autorizados a conter dados controlados pela exportação. Não introduza dados regulados/controlados nos seguintes campos: **Nome cache, nome de subscrição, grupos de recursos, etiquetas de recursos, propriedades Redis**. |

## <a name="azure-database-for-postgresql"></a>Base de Dados do Azure para PostgreSQL
Para mais informações sobre este serviço e como utilizá-lo, consulte a Base de [Dados Azure para documentação PostgreSQL](../postgresql/index.yml).

### <a name="variations"></a>Variações
A Proteção Avançada de Ameaças, insights de desempenho de consulta e recomendações de desempenho para a Base de Dados Azure para PostgreSQL **não** estão disponíveis no Governo Azure.

Os URLs para aceder e gerir a Base de Dados Azure para postgresQL no Governo Azure são diferentes:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
| Ponto final pósgreSQL |*.postgres.database.azure.com |*.postgres.database.usgovcloudapi.net |

### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para a Base de Dados Azure para postgresQL:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e tratados na Base de Dados Azure para postgresQL podem conter dados regulados pelo Governo de Azure. Utilize ferramentas de base de dados para a transferência de dados de dados regulados pelo Governo do Azure. |A Base de Dados Azure para metadados PostgreSQL não está autorizada a conter dados controlados pela exportação. Estes metadados incluem todos os dados de configuração introduzidos na criação e manutenção do seu produto de armazenamento.  Não introduza dados regulados/controlados nos seguintes campos: Nome da base de dados, nome de subscrição, grupos de recursos, nome do Servidor, login de administração do Servidor, nomes de implementação, nomes de recursos, etiquetas de recursos. |

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
Para mais informações sobre este serviço e como utilizá-lo, consulte a Base de [Dados Azure para obter documentação MariaDB.](../mariadb/index.yml)

### <a name="variations"></a>Variações
As recomendações de desempenho e desempenho da Base de Dados Azure para o MariaDB **não** estão disponíveis no Governo Azure.

Os URLs para aceder e gerir a Base de Dados Azure para a MariaDB no Governo Azure são diferentes:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
| Ponto final mariaDB |*.mariadb.database.azure.com |*.mariadb.database.usgovcloudapi.net |

### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para a Base de Dados Azure para o MariaDB:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e tratados na Base de Dados Azure para mariaDB podem conter dados regulados pelo Governo do Azure. Utilize ferramentas de base de dados para a transferência de dados de dados regulados pelo Governo do Azure. |A Base de Dados Azure para metadados MariaDB não está autorizada a conter dados controlados pela exportação. Estes metadados incluem todos os dados de configuração introduzidos na criação e manutenção do seu produto de armazenamento.  Não introduza dados regulados/controlados nos seguintes campos: Nome da base de dados, nome de subscrição, grupos de recursos, nome do Servidor, login de administração do Servidor, nomes de implementação, nomes de recursos, etiquetas de recursos. |

## <a name="azure-database-for-mysql"></a>Base de Dados do Azure para MySQL
Para mais detalhes sobre este serviço e como usá-lo, consulte a Base de [Dados Azure para a documentação MySQL](../mysql/index.yml).

### <a name="variations"></a>Variações
A Proteção Avançada de Ameaças, insights de desempenho de consulta e recomendações de desempenho para a Base de Dados Azure para mySQL **não** estão disponíveis no Governo Azure.

Os URLs para aceder e gerir a Base de Dados Azure para o MySQL no Governo Azure são diferentes:

| Tipo de Serviço | Azure Public | Azure Government |
| --- | --- | --- |
| Ponto final do MySQL |*.mysql.database.azure.com |*.mysql.database.usgovcloudapi.net |

### <a name="considerations"></a>Considerações
As seguintes informações identificam a fronteira do Governo Azure para a Base de Dados Azure para o MySQL:

| Dados regulados/controlados permitidos | Dados regulados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e tratados na Base de Dados Azure para o MySQL podem conter dados regulados pelo Governo de Azure. Utilize ferramentas de base de dados para a transferência de dados de dados regulados pelo Governo do Azure. |A Base de Dados Azure para metadados MySQL não está autorizada a conter dados controlados pela exportação. Estes metadados incluem todos os dados de configuração introduzidos na criação e manutenção do seu produto de armazenamento.  Não introduza dados regulados/controlados nos seguintes campos: Nome da base de dados, nome de subscrição, grupos de recursos, nome do Servidor, login de administração do Servidor, nomes de implementação, nomes de recursos, etiquetas de recursos. |

## <a name="next-steps"></a>Passos seguintes
Para informações e atualizações suplementares subscreva o <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>
