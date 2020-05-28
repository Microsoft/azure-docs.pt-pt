---
title: Upgrade para a mais recente biblioteca de clientes de base de dados elástica
description: Use o NuGet para atualizar a biblioteca de clientes de base de dados elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 8610342c1d01deceebaf1f4998dd04181e5ddc21
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050829"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualize uma aplicação para usar a mais recente biblioteca de clientes de base de dados elástica
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Novas versões da biblioteca de [clientes da Base](elastic-database-client-library.md) de Dados Elástica estão disponíveis através do NuGet e da interface NuGet Package Manager no Estúdio Visual. As atualizações contêm correções de bugs e suporte para novas capacidades da biblioteca de clientes.

**Para a versão mais recente:** Vá a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Reconstrua a sua aplicação com a nova biblioteca, bem como altere os metadados existentes do Shard Map Manager armazenados nas suas bases de dados na Base de Dados Azure SQL para suportar novas funcionalidades.

A realização destes passos garante que as versões antigas da biblioteca do cliente já não estão presentes no seu ambiente quando os objetos de metadados são atualizados, o que significa que os objetos de metadados da versão antiga não serão criados após a atualização.

## <a name="upgrade-steps"></a>Etapas de upgrade

**1. Atualize as suas aplicações.** No Visual Studio, descarregue e refira a mais recente versão da biblioteca de clientes em todos os seus projetos de desenvolvimento que utilizam a biblioteca; em seguida, reconstruir e implantar.

* Na sua solução Visual Studio, selecione **Tools**  -->  **NuGet Package Manager**Manage  -->   **NuGet Packages for Solution**.
* (Estúdio Visual 2013) No painel esquerdo, selecione **Atualizações**, e, em seguida, selecione o botão **'Actualização'** da **embalagem Azure SQL Database Elastic Scale Client Library** que aparece na janela.
* (Estúdio Visual 2015) Desloque a caixa de filtro para **atualizar disponível**. Selecione o pacote para atualizar e clique no botão **'Actualizar'.**
* (Estúdio Visual 2017) Na parte superior do diálogo, selecione **Atualizações**. Selecione o pacote para atualizar e clique no botão **'Actualizar'.**
* Construir e Implementar.

**2. Atualize os seus scripts.** Se estiver a usar scripts **PowerShell** para gerir fragmentos, [descarregue a nova versão da biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copie-a para o diretório a partir do qual executa scripts.

**3. Atualize o seu serviço de fusão de divisão.** Se utilizar a ferramenta de fusão de base de dados elástica para reorganizar dados fragmentos, [faça o download e implemente a versão mais recente da ferramenta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Os passos de upgrade detalhados para o Serviço podem ser encontrados [aqui](elastic-scale-overview-split-and-merge.md).

**4. Atualize as bases**de dados do Shard Map Manager . Atualize os metadados suportando o seu Shard Maps na Base de Dados SQL azure.  Existem duas maneiras de o conseguires, usando powerShell ou C#. Ambas as opções são mostradas abaixo.

***Opção 1: Atualizar metadados utilizando powerShell***

1. Descarregue o mais recente utilitário de linha de comando para NuGet a partir [daqui](https://nuget.org/nuget.exe) e guarde para uma pasta.
2. Abra um Pedido de Comando, navegue para a mesma pasta e emita o comando:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navegue para a subpasta que contém a nova versão DLL do cliente que acaba de descarregar, por exemplo:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Descarregue o script de atualização do cliente de base de dados elástica do [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e guarde-o na mesma pasta contendo o DLL.
5. A partir dessa pasta, execute "PowerShell .\upgrade.ps1" a partir do pedido de comando e siga as instruções.

***Opção 2: Atualizar metadados utilizando C #***

Alternativamente, crie uma aplicação do Estúdio Visual que abra o seu ShardMapManager, iterates sobre todos os fragmentos, e executa a atualização de metadados, chamando os métodos [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) e [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) como neste exemplo:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Estas técnicas para atualizações de metadados podem ser aplicadas várias vezes sem danos. Por exemplo, se uma versão de cliente mais antiga criar inadvertidamente um fragmento depois de já ter atualizado, pode fazer a atualização novamente em todos os fragmentos para garantir que a versão mais recente dos metadados está presente em toda a sua infraestrutura.

**Nota:**  As novas versões da biblioteca de clientes publicadas até à data continuam a funcionar com versões anteriores dos metadados do Shard Map Manager na Base de Dados Azure SQL, e vice-versa.   No entanto, para aproveitar algumas das novidades no cliente mais recente, os metadados precisam de ser atualizados.   Note que as atualizações de metadados não afetarão quaisquer dados específicos do utilizador ou da aplicação, apenas objetos criados e utilizados pelo Shard Map Manager.  E as aplicações continuam a funcionar através da sequência de atualização descrita acima.

## <a name="elastic-database-client-version-history"></a>Histórico de versão de cliente de base de dados elástica

Para o histórico da versão, vá a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
