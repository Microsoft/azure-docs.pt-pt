---
title: Começar com Ferramentas elásticas de base de dados
description: Explicação básica da funcionalidade Elástico database Tools da Azure SQL Database, incluindo uma aplicação de amostra fácil de executar.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 46eee878ba8cf1d12edf262241d5df8ef8ccc82f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442934"
---
# <a name="get-started-with-elastic-database-tools"></a>Começar com Ferramentas elásticas de base de dados
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este documento apresenta-lhe a experiência do desenvolvedor para a [biblioteca de clientes de base de dados elástica,](elastic-database-client-library.md) ajudando-o a executar uma aplicação de amostra. A aplicação de amostra cria uma aplicação simples e sharded e explora as capacidades-chave da funcionalidade Elástico Base de Dados de Dados sqL da Base de Dados Azure. Centra-se na utilização de casos [de gestão de mapas de](elastic-scale-shard-map-management.md) [fragmentos, encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md)e [consulta de vários fragmentos.](elastic-scale-multishard-querying.md) A biblioteca cliente está disponível para .NET, bem como java.

## <a name="elastic-database-tools-for-java"></a>Ferramentas elásticas de base de dados para Java

### <a name="prerequisites"></a>Pré-requisitos

* Um Kit de Desenvolvimento Java (JDK), versão 1.8 ou mais tarde
* [Maven](https://maven.apache.org/download.cgi)
* Base de Dados SQL ou uma instância local do Servidor SQL

### <a name="download-and-run-the-sample-app"></a>Descarregue e execute o aplicativo de amostra

Para construir os ficheiros JAR e começar com o projeto de amostra, faça o seguinte:

1. Clone o [repositório GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) contendo a biblioteca do cliente, juntamente com a aplicação da amostra.

2. Editar o ficheiro _./sample/src/main/resources/resource.properties_ para definir o seguinte:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Para construir o projeto de amostra, no _diretório ./amostra,_ executar o seguinte comando:

    ```
    mvn install
    ```

4. Para iniciar o projeto de amostra, no _diretório ./amostra,_ executar o seguinte comando:

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. Para saber mais sobre as capacidades da biblioteca do cliente, experimente as várias opções. Sinta-se livre para explorar o código para saber sobre a implementação da aplicação da amostra.

    ![Progresso-java][5]

Parabéns! Construiu e executou com sucesso a sua primeira aplicação de sharded utilizando Ferramentas elásticas de base de dados na Base de Dados Azure SQL. Utilize o Visual Studio ou o SQL Server Management Studio para ligar à sua base de dados e dar uma olhada rápida nos fragmentos que a amostra criou. Irá notar novas bases de dados de fragmentos de amostra e uma base de dados de gestor de mapas de fragmentos que a amostra criou.

Para adicionar a biblioteca do cliente ao seu próprio projeto Maven, adicione a seguinte dependência no seu ficheiro POM:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>Ferramentas de base de dados elásticas para .NET

### <a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2012 ou mais tarde com C#. Descarregue uma versão gratuita no [Visual Studio Downloads.](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* NuGet 2.7 ou mais tarde. Para obter a versão mais recente, consulte [instalar o NuGet.](https://docs.nuget.org/docs/start-here/installing-nuget)

### <a name="download-and-run-the-sample-app"></a>Descarregue e execute o aplicativo de amostra

Para instalar a biblioteca, aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A biblioteca está instalada com a aplicação de amostras descrita na secção seguinte.

Para descarregar e executar a amostra, siga estes passos:

1. Descarregue as [Ferramentas Elásticas DB para Azure SQL - Obter Amostra de Início](https://github.com/Azure/elastic-db-tools). Desaperte a amostra para um local que escolher.

2. Para criar um projeto, abra a solução *ElasticScaleStarterKit.sln* a partir do diretório *C#.*

3. Na solução para o projeto da amostra, abra o ficheiro *app.config.* Em seguida, siga as instruções do ficheiro para adicionar o nome do servidor e as informações de inscrição (nome de utilizador e palavra-passe).

4. Compile e execute a aplicação. Quando for solicitado, ative o Visual Studio para restaurar os pacotes NuGet da solução. Esta ação descarrega a versão mais recente da biblioteca de clientes de base de dados elástica do NuGet.

5. Para saber mais sobre as capacidades da biblioteca do cliente, experimente as várias opções. Note os passos que a aplicação toma na saída da consola e sinta-se livre para explorar o código nos bastidores.

   ![Progresso][4]

Parabéns! Construiu e executou com sucesso a sua primeira aplicação de sharded utilizando Ferramentas elásticas de base de dados na Base de Dados SQL. Utilize o Visual Studio ou o SQL Server Management Studio para ligar à sua base de dados e dar uma olhada rápida nos fragmentos que a amostra criou. Irá notar novas bases de dados de fragmentos de amostra e uma base de dados de gestor de mapas de fragmentos que a amostra criou.

> [!IMPORTANT]
> Recomendamos que utilize sempre a versão mais recente do Management Studio para que se mantenha sincronizado com atualizações para a Azure e a SQL Database. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Peças-chave da amostra de código

* **Gestão de fragmentos e mapas de fragmentos**: O código ilustra como trabalhar com fragmentos, gamas e mapeamentos no ficheiro *ShardManagementUtils.cs.* Para obter mais informações, consulte [bases de dados de escala com o gestor de mapas de fragmentos.](https://go.microsoft.com/?linkid=9862595)  

* **Encaminhamento dependente de dados**: O encaminhamento de transações para o fragmento certo é indicado no ficheiro *DataDependentRoutingSample.cs.* Para obter mais informações, consulte [o encaminhamento dependente de dados](https://go.microsoft.com/?linkid=9862596).

* **Consulta sobre vários fragmentos**: A consulta através de fragmentos é ilustrada no ficheiro *MultiShardQuerySample.cs.* Para obter mais informações, consulte [a consulta multi-fragmentos.](https://go.microsoft.com/?linkid=9862597)

* **Adicionar fragmentos vazios**: A adição iterativa de novos fragmentos vazios é efetuada pelo código no ficheiro *CreateShardSample.cs.* Para obter mais informações, consulte [bases de dados de escala com o gestor de mapas de fragmentos.](https://go.microsoft.com/?linkid=9862595)

## <a name="other-elastic-scale-operations"></a>Outras operações à escala elástica

* **Divisão de um fragmento existente**: A capacidade de dividir fragmentos é fornecida pela ferramenta de fusão dividida. Para obter mais informações, consulte [dados de deslocação entre bases de dados de nuvem escalonadas](elastic-scale-overview-split-and-merge.md).

* **Os fragmentos existentes**também são efetuados através da ferramenta de fusão de divisões. Para obter mais informações, consulte [dados de deslocação entre bases de dados de nuvem escalonadas](elastic-scale-overview-split-and-merge.md).

## <a name="cost"></a>Cost

A biblioteca Elástico Database Tools é gratuita. Quando utiliza Ferramentas elásticas de base de dados, não incorre em custos adicionais para além do custo da sua utilização do Azure.

Por exemplo, a aplicação da amostra cria novas bases de dados. O custo desta capacidade depende da edição SQL Database que escolher e da utilização do Azure da sua aplicação.

Para obter informações sobre preços, consulte [os detalhes dos preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as Ferramentas de Base de Dados Elásticas, consulte os seguintes artigos:

* Amostras de código:
  * Ferramentas elásticas de base de dados[(.NET,](https://github.com/Azure/elastic-db-tools) [Java)](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22)
  * [Ferramentas elásticas de base de dados para Azure SQL - Integração de Quadros de Entidade](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticidade do fragmento no Centro de Script](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Anúncio de Escala Elástica](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Canal 9: Vídeo [de visão geral da escala elástica](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum de discussão: [Microsoft Q&Uma página de perguntas para Azure SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html)
* Para medir o desempenho: [Contadores de desempenho para o gestor de mapas de fragmentos](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG
