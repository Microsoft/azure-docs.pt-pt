---
title: Contadores de desempenho para rastrear o gestor de mapas de fragmentos
description: Balcões de desempenho de encaminhamento shardMapManager e dados dependentes
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: 3bfbf56b6e5f2be33b407945490531e6e2e8ac47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92781265"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Criar contadores de desempenho para acompanhar o desempenho do gestor de mapas de fragmentos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os contadores de desempenho são utilizados para acompanhar o desempenho das operações de [encaminhamento dependentes de dados.](elastic-scale-data-dependent-routing.md) Estes contadores estão acessíveis no Monitor de Desempenho, na categoria "Base de Dados Elástica: Gestão de Fragmentos".

Pode capturar o desempenho de um [gestor de mapas de fragmentos,](elastic-scale-shard-map-management.md)especialmente quando utilizar [o encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md). Os contadores são criados com métodos da classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Para a versão mais recente:** Vá ao [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [uma aplicação para utilizar a mais recente biblioteca de clientes de base de dados elástica.](elastic-scale-upgrade-client-library.md)

## <a name="prerequisites"></a>Pré-requisitos

* Para criar a categoria de desempenho e os contadores, o utilizador deve fazer parte do grupo de **Administradores** locais na máquina que acolhe a aplicação.  
* Para criar uma contra-instância de desempenho e atualizar os contadores, o utilizador deve ser membro do grupo **de Administradores** ou utilizadores do Monitor de **Desempenho.**

## <a name="create-performance-category-and-counters"></a>Criar categoria de desempenho e contadores

Para criar os contadores, ligue para o método CreatePerformanceCategoryAndCounters da [classe ShardMapManagementFactory](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Apenas um administrador pode executar o método:

`ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()`

Também pode utilizar [este](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script PowerShell para executar o método.
O método cria os seguintes contadores de desempenho:  

* **Mapeamentos em** cache : Número de mapeamentos em cache para o mapa de fragmentos.
* **Operações DDR/seg**: Taxa de operações de encaminhamento dependentes de dados para o mapa de fragmentos. Este contador é atualizado quando uma chamada para [OpenConnectionForKey()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulta numa ligação bem sucedida ao fragmento de destino.
* **Mapeamento de cache hits/seg**: Taxa de operações de procura de cache bem sucedidas para mapeamentos no mapa de fragmentos.
* **Mapeamento de cache falha/seg**: Taxa de operações de procura de cache falhadas para mapeamentos no mapa de fragmentos.
* **Mapeamentos adicionados ou atualizados em cache/seg**: Taxa em que os mapeamentos são adicionados ou atualizados em cache para o mapa de fragmentos.
* **Mapeamentos removidos da cache/seg**: Taxa em que os mapeamentos estão a ser removidos da cache para o mapa de fragmentos.

Os contadores de desempenho são criados para cada mapa de fragmentos em cache por processo.  

## <a name="notes"></a>Notas

Os seguintes eventos desencadeiam a criação dos contadores de desempenho:  

* Inicialização do [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) com [carregamento ansioso,](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)se o ShardMapManager contiver quaisquer mapas de fragmentos. Estes incluem os [métodos GetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) e os métodos [TryGetSqlShardMapManager.](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager)
* Procura com sucesso de um mapa de fragmentos (utilizando [GetShardMap()](/previous-versions/azure/dn824215(v=azure.100)), [GetListShardMap()](/previous-versions/azure/dn824212(v=azure.100)) ou [GetRangeShardMap()](/previous-versions/azure/dn824173(v=azure.100))).
* Criação bem sucedida de mapa de fragmentos utilizando o CreateShardMap().

Os contadores de desempenho serão atualizados por todas as operações de cache realizadas no mapa de fragmentos e mapeamentos. A remoção bem sucedida do mapa de fragmentos utilizando deleteShardMap() resulta na eliminação da instância dos contadores de desempenho.  

## <a name="best-practices"></a>Melhores práticas

* A criação da categoria de desempenho e dos contadores só deve ser realizada uma vez antes da criação do objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anteriores (perdendo dados reportados por todos os casos) e cria novos.  
* As contra-instâncias de desempenho são criadas por processo. Qualquer falha de aplicação ou remoção de um mapa de fragmentos da cache resultará na eliminação das instâncias dos contadores de desempenho.  

### <a name="see-also"></a>Ver também

[Descrição geral das funcionalidades da Base de Dados Elástica](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->