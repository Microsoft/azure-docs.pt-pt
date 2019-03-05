---
title: Introdução às ferramentas de base de dados elásticas - Azure | Documentos da Microsoft
description: EXPLICAÇÃO básica da funcionalidade ferramentas de base de dados elásticas do SQL Database do Azure, incluindo uma aplicação de exemplo simples para executar.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 245cbafb42becc230f5e480cfc8e6fa41c352190
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309388"
---
# <a name="get-started-with-elastic-database-tools"></a>Introdução às ferramentas de base de dados elásticas

Este documento apresenta a a experiência do desenvolvedor para o [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md) , ajudando a executar uma aplicação de exemplo. A aplicação de exemplo cria uma aplicação fragmentada simples e explora os principais capacidades da funcionalidade ferramentas de base de dados elásticas da base de dados do Azure SQL. Ele se concentra em casos de utilização [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md), [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md), e [consultas de vários fragmentos](sql-database-elastic-scale-multishard-querying.md). A biblioteca de clientes está disponível para .NET, bem como a Java. 

## <a name="elastic-database-tools-for-java"></a>Ferramentas de bases de dados elásticas para Java

### <a name="prerequisites"></a>Pré-requisitos

* Um desenvolvedor Kit (JDK do Java), versão 1.8 ou posterior
* [Maven](http://maven.apache.org/download.cgi)
* Um servidor de base de dados SQL no Azure ou uma instância local do SQL Server

### <a name="download-and-run-the-sample-app"></a>Transfira e execute a aplicação de exemplo

Para criar os ficheiros JAR e começar a utilizar com o projeto de exemplo, faça o seguinte: 
1. Clone o [repositório do GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) que contém a biblioteca de cliente, juntamente com a aplicação de exemplo. 

2. Editar a _./sample/src/main/resources/resource.properties_ ficheiro para definir o seguinte:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Para compilar o projeto de exemplo, além da _. / de exemplo_ diretório, execute o seguinte comando:

    ```
    mvn install
    ```
    
4. Para iniciar o projeto de exemplo, na _. / de exemplo_ diretório, execute o seguinte comando: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Para saber mais sobre os recursos de biblioteca de cliente, experimente as várias opções. Fique à vontade explorar o código para saber mais sobre a implementação de aplicação de exemplo.

    ![Progress-java][5]
    
Parabéns! Ter criado e execute a sua primeira aplicação em partição horizontal, utilizando ferramentas de base de dados elásticas na base de dados do Azure SQL com êxito. Use o Visual Studio ou o SQL Server Management Studio para ligar à base de dados SQL e dar uma olhada rápida as partições horizontais que o exemplo é criado. Notará novas bases de dados de partição horizontal de exemplo e uma base de Gestor de mapa de partições horizontais que tiver criado o exemplo. 

Para adicionar a biblioteca de cliente para o seu próprio projeto Maven, adicione a seguinte dependência no ficheiro POM:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Ferramentas de bases de dados elásticas para .NET

### <a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2012 ou posterior com c#. Baixe uma versão gratuita na [transferências do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 ou posterior. Para obter a versão mais recente, consulte [instalar NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Transfira e execute a aplicação de exemplo

Para instalar a biblioteca, aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A biblioteca é instalada com a aplicação de exemplo descrita na secção seguinte.

Para transferir e executar o exemplo, siga estes passos: 

1. Transfira o [ferramentas elásticas de DB para SQL do Azure - introdução ao exemplo](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) do MSDN. Deszipe o exemplo para uma localização que escolher.

2. Para criar um projeto, abra a *ElasticScaleStarterKit.sln* solução a partir do *c#* diretório.

3. Na solução do projeto de exemplo, abra a *App. config* ficheiro. Em seguida, siga as instruções no ficheiro para adicionar o nome do servidor de base de dados do Azure SQL e suas informações início de sessão (nome de utilizador e palavra-passe).

4. Crie e execute a aplicação. Quando lhe for pedido, ative o Visual Studio restaurar os pacotes de NuGet da solução. Esta ação transfere a versão mais recente da biblioteca de cliente de base de dados elástica do NuGet.

5. Para saber mais sobre os recursos de biblioteca de cliente, experimente as várias opções. Tenha em atenção os passos que o aplicativo usa no console de saída e à vontade explorar o código por trás dos bastidores.
   
    ![Progresso][4]

Parabéns! Ter criado e execute a sua primeira aplicação em partição horizontal, utilizando ferramentas de base de dados elásticas na base de dados SQL com êxito. Use o Visual Studio ou o SQL Server Management Studio para ligar à base de dados SQL e dar uma olhada rápida as partições horizontais que o exemplo é criado. Notará novas bases de dados de partição horizontal de exemplo e uma base de Gestor de mapa de partições horizontais que tiver criado o exemplo.

> [!IMPORTANT]
> Recomendamos que utilize sempre a versão mais recente do Management Studio para que mantidos em sincronia com as atualizações do Azure e base de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Principais partes do código de exemplo

* **Gerenciamento de partições horizontais e partição horizontal mapeia**: O código ilustra como trabalhar com partições horizontais, intervalos e mapeamentos no *ShardManagementUtils.cs* ficheiro. Para obter mais informações, consulte [aumentar horizontalmente de bases de dados com o Gestor de mapas de partições horizontais](https://go.microsoft.com/?linkid=9862595).  

* **Encaminhamento dependente de dados**: Encaminhamento de transações para a partição horizontal direito mostra as *DataDependentRoutingSample.cs* ficheiro. Para obter mais informações, consulte [encaminhamento dependente de dados](https://go.microsoft.com/?linkid=9862596). 

* **Consultando a várias partições horizontais**: Consultas entre partições horizontais é ilustrada na *MultiShardQuerySample.cs* ficheiro. Para obter mais informações, consulte [consultas de vários fragmentos](https://go.microsoft.com/?linkid=9862597).

* **Adicionar partições horizontais vazias**: A adição iterativo de novas partições horizontais de vazias é realizada pelo código dos *CreateShardSample.cs* ficheiro. Para obter mais informações, consulte [aumentar horizontalmente de bases de dados com o Gestor de mapas de partições horizontais](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Outras operações de dimensionamento flexível

* **A divisão de uma partição horizontal existente**: A capacidade de dividir as partições horizontais é fornecida pela ferramenta de divisão / intercalação. Para obter mais informações, consulte [mover dados entre bases de dados de cloud aumentadas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md).

* **Intercalação de partições horizontais existentes**: Partição horizontal mesclagens também são executadas utilizando a ferramenta de divisão / intercalação. Para obter mais informações, consulte [mover dados entre bases de dados de cloud aumentadas horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Custo

A biblioteca de ferramentas de base de dados elásticas é gratuita. Ao utilizar ferramentas de base de dados elásticas, não implica custos adicionais além do custo da sua utilização do Azure. 

Por exemplo, o aplicativo de exemplo cria novas bases de dados. O custo desta capacidade depende da edição de base de dados SQL que escolher e a utilização do Azure da sua aplicação.

Para obter informações sobre preços, consulte [base de dados SQL os detalhes dos preços](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre ferramentas de base de dados elásticas, consulte os artigos seguintes:

* Exemplos de código: 
  * Ferramentas de base de dados elástica ([.NET](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Ferramentas de bases de dados elásticas para SQL do Azure - integração de Entity Framework](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticidade de fragmentos no Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blogue: [Anúncio de escala elástico](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [Elástico dimensionamento vídeo de descrição geral](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum de discussão: [Fórum de base de dados SQL do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Para medir o desempenho: [Contadores de desempenho do gestor de mapas de partições horizontais](sql-database-elastic-database-client-library.md)

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

