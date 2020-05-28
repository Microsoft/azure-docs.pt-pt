---
title: Contadores de desempenho para rastrear o gestor de mapas de fragmentos
description: Classe ShardMapManager e contadores de desempenho dependentes de dados
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: a4e97fc6b46127dbc6b047e897dadf0efa76c94b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050199"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Crie contadores de desempenho para acompanhar o desempenho do gestor de mapas de fragmentos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os contadores de desempenho são utilizados para acompanhar o desempenho das operações de [encaminhamento dependentes](elastic-scale-data-dependent-routing.md) de dados. Estes contadores são acessíveis no Monitor de Desempenho, na categoria "Base de Dados Elástica: Gestão de Fragmentos".

Pode capturar o desempenho de um gestor de mapas de [fragmentos,](elastic-scale-shard-map-management.md)especialmente quando utilizar o [encaminhamento dependente](elastic-scale-data-dependent-routing.md)de dados . Os contadores são criados com métodos da classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Para a versão mais recente:** Vá a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [a atualização de uma aplicação para utilizar a mais recente biblioteca](elastic-scale-upgrade-client-library.md)de clientes de base de dados elástica.

## <a name="prerequisites"></a>Pré-requisitos

* Para criar a categoria de desempenho e os contadores, o utilizador deve fazer parte do grupo **de Administradores locais** na máquina que acolhe a aplicação.  
* Para criar uma contra-instância de desempenho e atualizar os contadores, o utilizador deve ser membro do grupo **Deadministradores** ou utilizadores do Monitor de **Desempenho.**

## <a name="create-performance-category-and-counters"></a>Criar categoria de desempenho e contadores

Para criar os contadores, ligue para o método CreatePerformanceCategoryAndCounters da [classe ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Apenas um administrador pode executar o método:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Também pode utilizar [este](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script PowerShell para executar o método.
O método cria os seguintes contadores de desempenho:  

* **Mapeamentos em cache**: Número de mapeamentos em cache para o mapa do fragmento.
* **Operações DDR/seg**: Taxa de operações de encaminhamento dependente de dados para o mapa do fragmento. Este contador é atualizado quando uma chamada para [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulta numa ligação bem sucedida ao fragmento de destino.
* **Mapeando os hits/sec**de montagem de cache : Taxa de operações de procura de cache bem sucedidas para mapeamentono mapa.
* **Mapeando**a cache de procura de procura perde/seg : Taxa de operações de procura de cache falhadas para mapeamentono mapa.
* **Mapeamentos adicionados ou atualizados em cache/seg**: Taxa a que os mapeamentos estão a ser adicionados ou atualizados em cache para o mapa do fragmento.
* **Mapeamentos removidos do cache/seg**: Taxa a que os mapeamentos estão a ser removidos da cache para o mapa do fragmento.

Os contadores de desempenho são criados para cada mapa de fragmentos em cache por processo.  

## <a name="notes"></a>Notas

Os seguintes eventos desencadeiam a criação dos contadores de desempenho:  

* Inicialização do [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) com [carregamento ansioso](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), se o ShardMapManager contiver quaisquer mapas de fragmentos. Estes incluem os [métodos GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) e [TryGetSqlShardMapManager.](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager)
* Procura bem-sucedida de um mapa de fragmentos (utilizando [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx).
* Criação bem-sucedida de mapa de fragmentos utilizando o CreateShardMap().

Os contadores de desempenho serão atualizados por todas as operações de cache realizadas no mapa do fragmento e mapeamentos. A remoção bem sucedida do mapa do fragmento utilizando o DeleteShardMap resulta na eliminação da instância dos contadores de desempenho.  

## <a name="best-practices"></a>Melhores práticas

* A criação da categoria de desempenho e dos balcões só deve ser realizada uma vez antes da criação do objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anteriores (perda de dados reportados por todos os casos) e cria novos.  
* As contra-instâncias de desempenho são criadas por processo. Qualquer falha de aplicação ou remoção de um mapa de fragmentos da cache resultará na eliminação das instâncias dos contadores de desempenho.  

### <a name="see-also"></a>Veja também

[Descrição geral das funcionalidades da Base de Dados Elástica](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
