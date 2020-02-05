---
title: Começar com ferramentas de base de dados elásticas
description: Explicação básica da funcionalidade ferramentas de base de dados elásticas da Base de Dados Azure SQL, incluindo uma aplicação de amostra fácil de executar.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 4577f611287a27366b1168e07565861c16fe0e38
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987027"
---
# <a name="get-started-with-elastic-database-tools"></a>Começar com ferramentas de base de dados elásticas

Este documento apresenta-lhe a experiência do desenvolvedor para a biblioteca de clientes de base de [dados elástica,](sql-database-elastic-database-client-library.md) ajudando-o a executar uma aplicação de amostra. A aplicação de amostra sharded cria uma aplicação simples e explora as principais capacidades da funcionalidade Ferramentas de Base de Dados Elásticas da Base de Dados Azure SQL. Centra-se na utilização de casos para [a gestão](sql-database-elastic-scale-shard-map-management.md)de mapas de fragmentos, [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)e consulta [multi-fragmentos.](sql-database-elastic-scale-multishard-querying.md) A biblioteca de clientes está disponível para .NET e Java. 

## <a name="elastic-database-tools-for-java"></a>Ferramentas de base de dados elásticas para Java

### <a name="prerequisites"></a>Pré-requisitos

* Um Kit de Desenvolvimento Java (JDK), versão 1.8 ou posterior
* [Maven](https://maven.apache.org/download.cgi)
* Um servidor de base de dados SQL em Azure ou uma instância local do Servidor SQL

### <a name="download-and-run-the-sample-app"></a>Descarregue e execute a aplicação de amostra

Para construir os ficheiros JAR e começar com o projeto da amostra, faça o seguinte: 
1. Clone o [repositório GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) contendo a biblioteca do cliente, juntamente com a aplicação de amostra. 

2. Editar o ficheiro _./sample/src/main/resources/resource.properties_ para definir o seguinte:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Para construir o projeto de amostragem, no diretório _./amostra,_ executar o seguinte comando:

    ```
    mvn install
    ```
    
4. Para iniciar o projeto de amostragem, no diretório _./amostra,_ executar o seguinte comando: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Para saber mais sobre as capacidades da biblioteca do cliente, experimente as várias opções. Sinta-se livre para explorar o código para saber sobre a implementação da aplicação de amostras.

    ![Progresso-java][5]
    
Parabéns! Construiu e executou com sucesso a sua primeira aplicação esforçada utilizando ferramentas de base de dados elásticas na Base de Dados SQL Azure. Utilize o Visual Studio ou o SQL Server Management Studio para se ligar à sua base de dados SQL e dar uma olhada rápida nos fragmentos que a amostra criou. Você vai notar novas bases de dados de fragmentos de amostra e uma base de dados de gestor de mapas de fragmentos que a amostra criou. 

Para adicionar a biblioteca de clientes ao seu próprio projeto Maven, adicione a seguinte dependência no seu ficheiro POM:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Ferramentas de base de dados elásticas para .NET

### <a name="prerequisites"></a>Pré-requisitos

* Estúdio Visual 2012 C#ou mais tarde com . Faça o download de uma versão gratuita no [Visual Studio Downloads.](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* NuGet 2.7 ou mais tarde. Para obter a versão mais recente, consulte [Instalar nuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Descarregue e execute a aplicação de amostra

Para instalar a biblioteca, vá a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A biblioteca está instalada com a aplicação de amostras descrita na secção seguinte.

Para descarregar e executar a amostra, siga estes passos: 

1. Descarregue as [ferramentas ELÁSTICAS DB para Azure SQL - Começar](https://github.com/Azure/elastic-db-tools)a amostra . Desaperte a amostra para um local que escolher.

2. Para criar um projeto, abra a solução *ElasticScaleStarterStarter.sln* a *C#* partir do diretório.

3. Na solução para o projeto da amostra, abra o ficheiro *app.config.* Em seguida, siga as instruções no ficheiro para adicionar o nome do seu servidor de base de dados Azure SQL e as suas informações de início de sessão (nome de utilizador e palavra-passe).

4. Construa e execute a aplicação. Quando for solicitado, ative o Visual Studio a restaurar os pacotes NuGet da solução. Esta ação descarrega a versão mais recente da biblioteca de clientes de base de dados elástica da NuGet.

5. Para saber mais sobre as capacidades da biblioteca do cliente, experimente as várias opções. Note os passos que a aplicação toma na saída da consola e sinta-se livre para explorar o código nos bastidores.
   
    ![Progresso][4]

Parabéns! Construiu e executou com sucesso a sua primeira aplicação esforçada utilizando ferramentas de base de dados elásticas na Base de Dados SQL. Utilize o Visual Studio ou o SQL Server Management Studio para se ligar à sua base de dados SQL e dar uma olhada rápida nos fragmentos que a amostra criou. Você vai notar novas bases de dados de fragmentos de amostra e uma base de dados de gestor de mapas de fragmentos que a amostra criou.

> [!IMPORTANT]
> Recomendamos que utilize sempre a versão mais recente do Management Studio para que se mantenha sincronizado com atualizações para a Base de Dados Azure e SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Peças-chave da amostra de código

* **Gestão de fragmentos e mapas**de fragmentos : O código ilustra como trabalhar com fragmentos, gamas e mapeamentos no ficheiro *ShardManagementUtils.cs.* Para mais informações, consulte as bases de [dados scale out com o gestor de mapas de fragmentos](https://go.microsoft.com/?linkid=9862595).  

* **Encaminhamento dependente de dados**: O encaminhamento das transações para o fragmento direito é mostrado no ficheiro *DataDependentRoutingSample.cs.* Para obter mais informações, consulte o [encaminhamento dependente de dados](https://go.microsoft.com/?linkid=9862596). 

* **Consulta sobre vários fragmentos**: A consulta através de fragmentos é ilustrada no ficheiro *MultiShardQuerySample.cs.* Para mais informações, consulte a [consulta multi-fragmento](https://go.microsoft.com/?linkid=9862597).

* **Adição de fragmentos vazios**: A adição iterativa de novos fragmentos vazios é executada pelo código no ficheiro *CreateShardSample.cs.* Para mais informações, consulte as bases de [dados scale out com o gestor de mapas de fragmentos](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Outras operações de escala elástica

* **Dividir um fragmento existente**: A capacidade de dividir fragmentos é fornecida pela ferramenta de fusão de divisão. Para mais informações, consulte [os dados em movimento entre](sql-database-elastic-scale-overview-split-and-merge.md)bases de dados em nuvem .

* Fusão de **fragmentos existentes**: As fusões de fragmentos também são realizadas utilizando a ferramenta de fusão de divisão. Para mais informações, consulte [os dados em movimento entre](sql-database-elastic-scale-overview-split-and-merge.md)bases de dados em nuvem .   

## <a name="cost"></a>Custo

A biblioteca Elastic Database Tools é gratuita. Quando utiliza ferramentas de base de dados elásticas, não incorre em encargos adicionais para além do custo da sua utilização do Azure. 

Por exemplo, a aplicação da amostra cria novas bases de dados. O custo desta capacidade depende da edição da Base de Dados SQL que escolher e do uso azure da sua aplicação.

Para obter informações sobre preços, consulte os detalhes dos preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre ferramentas de base de dados elásticas, consulte os seguintes artigos:

* Amostras de código: 
  * Ferramentas de base de dados elásticas[(.NET,](https://github.com/Azure/elastic-db-tools) [Java)](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22)
  * [Ferramentas de base de dados elásticas para Azure SQL - Integração do Quadro de Entidades](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticidade do fragmento no Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Anúncio de Escala Elástica](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Canal 9: [Vídeo de visão geral da escala elástica](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum de discussão: Fórum de Base de [Dados Azure SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Para medir o desempenho: Contadores de [desempenho para gestor de mapas de fragmentos](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

