---
title: Upgrade para a mais recente biblioteca de clientes de base de dados elástica
description: Utilize o NuGet para atualizar a biblioteca de clientes de base de dados elástica.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793420"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualize uma aplicação para usar a mais recente biblioteca de clientes de base de dados elástica
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Novas versões da biblioteca de [clientes Elastic Database](elastic-database-client-library.md) estão disponíveis através do NuGet e da interface NuGet Package Manager em Visual Studio. As atualizações contêm correções de bugs e suporte para novas capacidades da biblioteca do cliente.

**Para a versão mais recente:** Vá ao [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Reconstrua a sua aplicação com a nova biblioteca, bem como altere os metadados existentes do Shard Map Manager armazenados nas suas bases de dados na Base de Dados Azure SQL para suportar novas funcionalidades.

A realização destes passos de forma a garantir que as versões antigas da biblioteca do cliente já não estão presentes no seu ambiente quando os objetos de metadados forem atualizados, o que significa que os objetos de metadados de versão antiga não serão criados após a atualização.

## <a name="upgrade-steps"></a>Atualizar passos

**1. Atualize as suas aplicações.** No Visual Studio, descarregue e refira a versão mais recente da biblioteca de clientes em todos os seus projetos de desenvolvimento que utilizam a biblioteca; em seguida, reconstruir e implantar.

* Na sua solução Visual Studio, selecione **Tools**  -->  **NuGet Package Manager**  -->   **Gerencie pacotes nuget para solução.**
* (Visual Studio 2013) No painel esquerdo, selecione **Atualizações** e, em seguida, selecione o botão **'Actualizar'** no pacote **Azure SQL Database Elastic Scale Client Library** que aparece na janela.
* (Visual Studio 2015) Coloque a caixa de filtro **disponível** para atualizar . Selecione o pacote para atualizar e clique no botão **'Atualizar'.**
* (Visual Studio 2017) No topo do diálogo, selecione **Updates**. Selecione o pacote para atualizar e clique no botão **'Atualizar'.**
* Construir e Implementar.

**2. Atualize os seus scripts.** Se estiver a utilizar scripts **PowerShell** para gerir fragmentos, [descarregue a nova versão](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) da biblioteca e copie-a para o diretório a partir do qual executa scripts.

**3. Atualize o seu serviço de fusão dividida.** Se utilizar a ferramenta de fusão de bases de dados elástica para reorganizar dados fragmentos, [descarregue e implemente a versão mais recente da ferramenta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Podem ser encontradas etapas de atualização detalhadas para o [Serviço.](elastic-scale-overview-split-and-merge.md)

**4. Atualize as bases de dados do Shard Map Manager**. Atualize os metadados que suportam os seus Shard Maps na Base de Dados Azure SQL.  Há duas maneiras de conseguir isto, usando PowerShell ou C#. Ambas as opções são mostradas abaixo.

***Opção 1: Atualizar metadados utilizando o PowerShell***

1. Descarregue o mais recente utilitário de linha de comando para NuGet a partir [daqui](https://nuget.org/nuget.exe) e guarde para uma pasta.
2. Abra uma solicitação de comando, navegue para a mesma pasta e emita o comando: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navegue para a sub-página que contém a versão DLL do novo cliente que acaba de descarregar, por exemplo: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Descarregue o script de atualização de clientes de base de dados elástica do [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e guarde-o na mesma pasta que contém o DLL.
5. A partir dessa pasta, executar "PowerShell .\upgrade.ps1" a partir da pronta de comando e siga as indicações.

***Opção 2: Atualizar metadados utilizando C #***

Em alternativa, crie uma aplicação Visual Studio que abra o seu ShardMapManager, itera sobre todos os fragmentos e executa a atualização de metadados chamando os métodos [UpgradeLocalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) e [UpgradeGlobalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) como neste exemplo:

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

Estas técnicas para atualizações de metadados podem ser aplicadas várias vezes sem danos. Por exemplo, se uma versão cliente mais antiga criar inadvertidamente um fragmento depois de já ter atualizado, pode executar novamente o upgrade em todos os fragmentos para garantir que a versão mais recente dos metadados está presente em toda a sua infraestrutura.

**Nota:**  As novas versões da biblioteca de clientes publicadas até à data continuam a funcionar com versões anteriores dos metadados Shard Map Manager na Base de Dados Azure SQL, e vice-versa.   No entanto, para tirar partido de algumas das novas funcionalidades do cliente mais recente, os metadados precisam de ser atualizados.   Note que as atualizações de metadados não afetarão quaisquer dados do utilizador ou dados específicos da aplicação, apenas objetos criados e utilizados pelo Shard Map Manager.  E as aplicações continuam a funcionar através da sequência de atualização acima descrita.

## <a name="elastic-database-client-version-history"></a>Histórico de versão de cliente de base de dados elástica

Para histórico da versão, vá ao [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png