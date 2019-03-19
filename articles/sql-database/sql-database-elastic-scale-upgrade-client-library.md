---
title: Atualizar para a biblioteca de cliente mais recente do bases de dados elásticas | Documentos da Microsoft
description: Utilize o Nuget para a biblioteca de cliente de atualização de bases de dados elásticas.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 02c09e5a2ad0c3e23fe641bf445a646477f7db2c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993975"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualizar uma aplicação para utilizar a biblioteca de cliente mais recente do bases de dados elásticas

Novas versões do [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md) estão disponíveis por meio de NuGetand interface do Gestor de NuGetPackage no Visual Studio. Atualizações contêm correções de erros e suportam para novos recursos da biblioteca de clientes.

**A versão mais recente:** Aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Reconstruir a aplicação com a nova biblioteca, bem como alterar os metadados do Gestor de mapas de partições horizontais existentes armazenados em seus bancos de dados SQL do Azure para oferecer suporte a novos recursos.

Efetuar estes passos por ordem assegura que as versões antigas da biblioteca de clientes já não estão presentes no seu ambiente quando os objetos de metadados são atualizados, que significa que os objetos de metadados de versão do antigo não serão criados após a atualização.

## <a name="upgrade-steps"></a>Passos de atualização

**1. Atualize seus aplicativos.** No Visual Studio, baixar e referenciar a versão de biblioteca de cliente mais recente em todos os projetos de desenvolvimento que utilizam a biblioteca; em seguida, reconstruir e implementar.

* Na sua solução do Visual Studio, selecione **ferramentas** --> **Gestor de pacotes NuGet** -->  **gerir pacotes NuGet para solução**.
* (O visual Studio 2013) No painel esquerdo, selecione **atualizações**e, em seguida, selecione a **Update** botão no pacote **SQL da base de dados elásticas dimensionamento biblioteca de clientes Azure** que aparece na janela do.
* (Visual Studio 2015) Defina a caixa de filtro para **atualizar disponíveis**. Selecione o pacote para atualizar e clique nas **atualizar** botão.
* (Visual Studio 2017) Na parte superior da caixa de diálogo, selecione **atualizações**. Selecione o pacote para atualizar e clique nas **atualizar** botão.
* Crie e implemente.

**2. Atualize seus scripts.** Se estiver a utilizar **PowerShell** scripts para gerir partições horizontais, [transferir a nova versão da biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copie-as para o diretório de onde executa scripts.

**3. Atualize o seu serviço de divisão / intercalação.** Se usar a ferramenta de dividir / unir da base de dados elástica para reorganizar os dados em partição horizontal, [baixar e implantar a versão mais recente da ferramenta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detalhadas etapas de atualização para o serviço pode ser encontrado [aqui](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Atualizar as suas bases de dados do Gestor de mapas de partições horizontais**. Atualize os metadados com suporte a seus mapas de partições horizontais na base de dados do Azure SQL.  Existem duas formas, é possível fazer isso, com o PowerShell ou c#. Ambas as opções são mostradas abaixo.

***Opção 1: Metadados de atualização com o PowerShell***

1. Transferir o utilitário da linha de comandos mais recente para NuGet partir [aqui](https://nuget.org/nuget.exe) e guarde numa pasta.
2. Abra uma linha de comandos, navegue para a mesma pasta e emitir o comando: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Navegue para a subpasta que contém a nova versão da DLL de cliente que tê acabou de transferir, por exemplo: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Transferir o script de atualização de cliente de base de dados elástica a partir da [Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e guarde-o para a mesma pasta que contém a DLL.
5. Nessa pasta, execute "PowerShell.\upgrade.ps1" na linha de comandos e siga as instruções.

***Opção 2: Atualizar com metadadosC#***

Em alternativa, criar um aplicativo do Visual Studio que abre seu ShardMapManager, itera em todas as partições horizontais e executa a atualização de metadados ao chamar os métodos [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) e [UpgradeGlobalStore ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) tal como neste exemplo:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Essas técnicas para atualizações de metadados podem ser aplicadas várias vezes sem prejuízo. Por exemplo, se uma versão mais antiga do cliente inadvertidamente cria uma partição horizontal depois de atualizar já, pode executar atualização novamente em todas as partições horizontais para garantir que a versão mais recente de metadados está presente em sua infra-estrutura.

**Nota:**  Até à data de publicação de novas versões da biblioteca de clientes continuar a trabalhar com versões anteriores dos metadados do Gestor de mapas de partições horizontais na BD SQL do Azure e vice-versa.   No entanto, para tirar partido de alguns dos novos recursos no cliente mais recente, metadados precisam ser atualizado.   Tenha em atenção que as atualizações de metadados não afetará quaisquer dados de utilizador ou a dados específicos de aplicativo, únicos objetos criados e usados pelo Gestor de mapas de partições horizontais.  E aplicações continuam a funcionar através de sequência de atualização descrita acima.

## <a name="elastic-database-client-version-history"></a>Histórico de versões de cliente de base de dados elástica

Para o histórico de versões, aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png