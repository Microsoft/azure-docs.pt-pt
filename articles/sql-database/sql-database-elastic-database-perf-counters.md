---
title: Criar contadores de desempenho para controlar o desempenho do Gestor de mapas de partições horizontais
description: ShardMapManager classe e de dados dependentes encaminhamento contadores de desempenho
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 5c6c923c86ea0c5968079188c87ec3988ec30142
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494923"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Criar contadores de desempenho para controlar o desempenho do Gestor de mapas de partições horizontais

Contadores de desempenho são utilizados para controlar o desempenho das [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) operações. Esses contadores são acessíveis no Monitor de desempenho no "bases de dados elásticas: Categoria de gestão de partições horizontais".

Pode capturar o desempenho de um [Gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md), especialmente quando se utilizam [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Contadores são criados com métodos da classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**A versão mais recente:** Aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [atualizar uma aplicação para utilizar a biblioteca de cliente mais recente do bases de dados elásticas](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para criar os contadores e categoria de desempenho, o utilizador tem de ser uma parte no local **administradores** grupo no computador que aloja a aplicação.  
* Para criar uma instância do contador de desempenho e atualizar os contadores, o utilizador tem de ser membro dos **administradores** ou **usuários de Monitor de desempenho** grupo.

## <a name="create-performance-category-and-counters"></a>Criar categoria de desempenho e contadores

Para criar os contadores, chamar o método de CreatePerformanceCategoryAndCounters do [ShardMapManagementFactory classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Apenas um administrador pode executar o método:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Também pode utilizar [isso](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script do PowerShell para executar o método.
O método cria os seguintes contadores de desempenho:  

* **Armazenados em cache mapeamentos**: Número de mapeamentos armazenada em cache para o mapa de partições horizontais.
* **Operações/seg DDR**: Taxa de operações encaminhamento dependente de dados para o mapa de partições horizontais. Este contador é atualizado quando uma chamada para [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulta numa ligação com êxito para a partição horizontal de destino.
* **Mapeamento de acertos de cache de pesquisa/seg**: Taxa de operações de pesquisa de cache com êxito para mapeamentos do mapa de partições horizontais.
* **Mapeamento de erros de cache de pesquisa/seg**: Taxa de operações de pesquisa de cache com falhas para mapeamentos do mapa de partições horizontais.
* **Mapeamentos adicionados ou atualizados no cache/seg**: Taxa na qual mapeamentos estão a ser adicionados ou atualizados na cache para o mapa de partições horizontais.
* **Mapeamentos removido do cache/seg**: Taxa à qual mapeamentos são removidos da cache para o mapa de partições horizontais.

Contadores de desempenho são criados para cada mapa de partições horizontais em cache por processo.  

## <a name="notes"></a>Notas

Os seguintes eventos acionam a criação dos contadores de desempenho:  

* Inicialização do [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) com [adiantado](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), se o ShardMapManager contém qualquer mapas de partições horizontais. Estes incluem os [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) e o [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) métodos.
* Pesquisa concluída com êxito de um mapa de partições horizontais (usando [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Criação bem-sucedida de mapa de partições horizontais com CreateShardMap().

Os contadores de desempenho serão atualizados por todas as operações de cache efetuadas no mapa de partições horizontais e mapeamentos. Remoção bem-sucedida do mapa de partições horizontais com DeleteShardMap() resulta na eliminação da instância de contadores de desempenho.  

## <a name="best-practices"></a>Melhores práticas

* Criação da categoria de desempenho e contadores deve ser efetuada apenas uma vez antes da criação do objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anteriores (perda de dados reportados por todas as instâncias) e cria novas etiquetas.  
* Instâncias de contadores de desempenho são criadas por processo. Qualquer falha do aplicativo ou a remoção de um mapa de partições horizontais da cache resultará na eliminação das instâncias de contadores de desempenho.  

### <a name="see-also"></a>Consulte também

[Descrição geral das funcionalidades de base de dados elásticas](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
