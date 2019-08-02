---
title: Atualizar para a biblioteca de cliente de banco de dados elástico mais recente | Microsoft Docs
description: Use o NuGet para atualizar a biblioteca de cliente do banco de dados elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 3d10814858d38d61e5346a4eb0dfb3d3d24ad4c0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568326"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualizar um aplicativo para usar a biblioteca de cliente de banco de dados elástico mais recente

Novas versões da [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) estão disponíveis por meio do NuGet e na interface do da Manager no Visual Studio. As atualizações contêm correções de bugs e suporte para novos recursos da biblioteca de cliente.

**Para obter a versão mais recente:** Vá para [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Recrie seu aplicativo com a nova biblioteca, bem como altere os metadados existentes do Gerenciador de mapa de fragmentos armazenados em seus bancos de dados SQL do Azure para dar suporte a novos recursos.

A execução dessas etapas na ordem garante que as versões antigas da biblioteca de cliente não estejam mais presentes em seu ambiente quando os objetos de metadados forem atualizados, o que significa que os objetos de metadados de versão antiga não serão criados após a atualização.

## <a name="upgrade-steps"></a>Etapas de atualização

**1. Atualize seus aplicativos.** No Visual Studio, baixe e referencie a versão mais recente da biblioteca do cliente em todos os seus projetos de desenvolvimento que usam a biblioteca; em seguida, recompile e implante.

* Na sua solução do Visual Studio, selecione **ferramentas** --> **Gerenciador** -->  de pacotes NuGet**gerenciar pacotes NuGet para solução**.
* (Visual Studio 2013) No painel esquerdo, selecione **atualizações**e, em seguida, selecione o botão **Atualizar** no pacote **biblioteca de cliente de escala elástica do banco de dados SQL do Azure** que aparece na janela.
* (Visual Studio 2015) Defina a caixa de filtro para **atualização disponível**. Selecione o pacote a ser atualizado e clique no botão **Atualizar** .
* (Visual Studio 2017) Na parte superior da caixa de diálogo, selecione **atualizações**. Selecione o pacote a ser atualizado e clique no botão **Atualizar** .
* Compilar e implantar.

**2. Atualize seus scripts.** Se você estiver usando scripts do **PowerShell** para gerenciar fragmentos, [Baixe a nova versão da biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copie-a no diretório do qual você executa scripts.

**3. Atualize seu serviço de divisão/mesclagem.** Se você usar a ferramenta de divisão/mesclagem de banco de dados elástico para reorganizar os dados fragmentados, [Baixe e implante a versão mais recente da ferramenta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). As etapas de atualização detalhadas para o serviço podem ser encontradas [aqui](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Atualize seus bancos de dados**do Gerenciador de mapa de fragmentos. Atualize os metadados que dão suporte aos mapas de fragmento no banco de dados SQL do Azure.  Há duas maneiras de fazer isso, usando o PowerShell ou C#o. As duas opções são mostradas abaixo.

***Opção 1: Atualizar metadados usando o PowerShell***

1. Baixe o utilitário de linha de comando mais recente para o NuGet [aqui](https://nuget.org/nuget.exe) e salve-o em uma pasta.
2. Abra um prompt de comando, navegue até a mesma pasta e emita o comando:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navegue até a subpasta que contém a nova versão de DLL de cliente que você acabou de baixar, por exemplo:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Baixe o script de atualização do cliente do banco de dados elástico no [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e salve-o na mesma pasta que contém a dll.
5. Nessa pasta, execute "PowerShell .\upgrade.ps1" no prompt de comando e siga os prompts.

***Opção 2: Atualizar metadados usandoC#***

Como alternativa, crie um aplicativo do Visual Studio que abra o ShardMapManager, itere sobre todos os fragmentos e execute a atualização de metadados chamando os métodos [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) e [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) como neste exemplo:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Essas técnicas para atualizações de metadados podem ser aplicadas várias vezes sem danos. Por exemplo, se uma versão de cliente mais antiga criar inadvertidamente um fragmento depois que você já tiver atualizado, você poderá executar a atualização novamente em todos os fragmentos para garantir que a versão de metadados mais recente esteja presente em toda a sua infraestrutura.

**Nota:**  As novas versões da biblioteca de cliente publicadas até a data continuam a funcionar com versões anteriores dos metadados do Gerenciador de mapa de fragmentos no banco de dados SQL do Azure e vice-versa.   No entanto, para aproveitar alguns dos novos recursos do cliente mais recente, os metadados precisam ser atualizados.   Observe que as atualizações de metadados não afetarão nenhum dado do usuário ou dados específicos do aplicativo, somente os objetos criados e usados pelo Gerenciador de mapa de fragmentos.  E os aplicativos continuam a operar por meio da sequência de atualização descrita acima.

## <a name="elastic-database-client-version-history"></a>Histórico de versão do cliente do banco de dados elástico

Para obter o histórico de versão, acesse [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png