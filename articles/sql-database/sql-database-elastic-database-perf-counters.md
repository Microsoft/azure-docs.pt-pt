---
title: Criar contadores de desempenho para acompanhar o desempenho do Gerenciador de mapa de fragmentos
description: Contadores de desempenho de roteamento dependente de dados e classe ShardMapManager
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568683"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Criar contadores de desempenho para acompanhar o desempenho do Gerenciador de mapa de fragmentos

Os contadores de desempenho são usados para acompanhar o desempenho de operações de [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) . Esses contadores podem ser acessados no monitor de desempenho, sob o "banco de dados elástico: Gerenciamento de fragmentos "categoria.

Você pode capturar o desempenho de um [Gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md), especialmente ao usar o [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Os contadores são criados com métodos da classe Microsoft. Azure. SQLDatabase. ElasticScale. Client.  


**Para obter a versão mais recente:** Vá para [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [atualizar um aplicativo para usar a biblioteca de cliente de banco de dados elástico mais recente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para criar a categoria de desempenho e os contadores, o usuário deve fazer parte do grupo local de **Administradores** no computador que hospeda o aplicativo.  
* Para criar uma instância do contador de desempenho e atualizar os contadores, o usuário deve ser membro do grupo **Administradores** ou **usuários do monitor de desempenho** .

## <a name="create-performance-category-and-counters"></a>Criar categoria e contadores de desempenho

Para criar os contadores, chame o método CreatePerformanceCategoryAndCounters da [classe ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Somente um administrador pode executar o método:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Você também pode usar [esse](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script do PowerShell para executar o método.
O método cria os seguintes contadores de desempenho:  

* **Mapeamentos em cache**: Número de mapeamentos armazenados em cache para o mapa de fragmentos.
* **Operações de DDR/s**: Taxa de operações de roteamento dependentes de dados para o mapa de fragmentos. Esse contador é atualizado quando uma chamada para [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulta em uma conexão bem-sucedida com o fragmento de destino.
* **Mapeando acertos do cache de pesquisa/s**: Taxa de operações de pesquisa de cache bem-sucedidas para mapeamentos no mapa de fragmentos.
* **Mapeando erros do cache de pesquisa/s**: Taxa de operações de pesquisa de cache com falha para mapeamentos no mapa de fragmentos.
* **Mapeamentos adicionados ou atualizados no cache/s**: Taxa em que os mapeamentos estão sendo adicionados ou atualizados no cache para o mapa de fragmentos.
* **Mapeamentos removidos do cache/s**: Taxa em que os mapeamentos estão sendo removidos do cache para o mapa de fragmentos.

Os contadores de desempenho são criados para cada mapa de fragmentos em cache por processo.  

## <a name="notes"></a>Notas

Os eventos a seguir disparam a criação dos contadores de desempenho:  

* Inicialização do [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) com [carregamento adiantado](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), se o ShardMapManager contiver qualquer mapa de fragmentos. Isso inclui os métodos [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) e [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) .
* Pesquisa com êxito de um mapa de fragmentos (usando [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Criação bem-sucedida do mapa de fragmentos usando CreateShardMap ().

Os contadores de desempenho serão atualizados por todas as operações de cache executadas no mapa de fragmentos e mapeamentos. A remoção bem-sucedida do mapa de fragmentos usando DeleteShardMap () resulta na exclusão da instância de contadores de desempenho.  

## <a name="best-practices"></a>Melhores práticas

* A criação da categoria de desempenho e dos contadores deve ser executada apenas uma vez antes da criação do objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters () limpa os contadores anteriores (perdendo dados relatados por todas as instâncias) e cria novos.  
* As instâncias do contador de desempenho são criadas por processo. Qualquer falha de aplicativo ou remoção de um mapa de fragmentos do cache resultará na exclusão das instâncias de contadores de desempenho.  

### <a name="see-also"></a>Consulte também

[Descrição geral das funcionalidades da Base de Dados Elástica](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
