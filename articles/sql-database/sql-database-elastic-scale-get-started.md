---
title: Introdução às ferramentas de banco de dados elástico – Azure | Microsoft Docs
description: Explicação básica do recurso de ferramentas de banco de dados elástico do banco de dados SQL do Azure, incluindo um aplicativo de exemplo fácil de executar.
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
ms.openlocfilehash: aa0827bdf6deb388123ed701013ee95f5493eee3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568466"
---
# <a name="get-started-with-elastic-database-tools"></a>Introdução às ferramentas de banco de dados elástico

Este documento apresenta a experiência do desenvolvedor para a [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) , ajudando você a executar um aplicativo de exemplo. O aplicativo de exemplo cria um aplicativo fragmentado simples e explora os principais recursos do recurso de ferramentas de banco de dados elástico do banco de dados SQL do Azure. Ele se concentra em casos de uso para [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md), [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)e [consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md). A biblioteca de cliente está disponível para .NET, bem como para Java. 

## <a name="elastic-database-tools-for-java"></a>Ferramentas de banco de dados elástico para Java

### <a name="prerequisites"></a>Pré-requisitos

* Um Java Developer Kit (JDK), versão 1,8 ou posterior
* [Maven](https://maven.apache.org/download.cgi)
* Um servidor de banco de dados SQL no Azure ou em uma instância de SQL Server local

### <a name="download-and-run-the-sample-app"></a>Baixar e executar o aplicativo de exemplo

Para criar os arquivos JAR e começar a usar o projeto de exemplo, faça o seguinte: 
1. Clone o [repositório GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) que contém a biblioteca de cliente, juntamente com o aplicativo de exemplo. 

2. Edite o arquivo _. arquivo/Sample/src/main/resources/Resource.Properties_ para definir o seguinte:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Para criar o projeto de exemplo, no diretório _./Sample_ , execute o seguinte comando:

    ```
    mvn install
    ```
    
4. Para iniciar o projeto de exemplo, no diretório _./Sample_ , execute o seguinte comando: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Para saber mais sobre os recursos da biblioteca de cliente, experimente as várias opções. Sinta-se à vontade para explorar o código para saber mais sobre a implementação do aplicativo de exemplo.

    ![Progresso-Java][5]
    
Parabéns! Você criou e executou com êxito seu primeiro aplicativo fragmentado usando as ferramentas de banco de dados elástico no banco de dados SQL do Azure. Use o Visual Studio ou SQL Server Management Studio para se conectar ao seu banco de dados SQL e dar uma olhada rápida nos fragmentos que o exemplo criou. Você notará novos bancos de dados de fragmentos de exemplo e um banco de dados do Gerenciador de mapa de fragmentos que o exemplo criou. 

Para adicionar a biblioteca de cliente ao seu próprio projeto Maven, adicione a seguinte dependência em seu arquivo POM:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Ferramentas de banco de dados elástico para .NET

### <a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2012 ou posterior com C#. Baixe uma versão gratuita em [downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2,7 ou posterior. Para obter a versão mais recente, consulte Instalando o [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Baixar e executar o aplicativo de exemplo

Para instalar a biblioteca, acesse [Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A biblioteca é instalada com o aplicativo de exemplo descrito na seção a seguir.

Para baixar e executar o exemplo, siga estas etapas: 

1. Baixe as [ferramentas de banco de BD elástico para o SQL do Azure-introdução exemplo](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) do MSDN. Descompacte o exemplo em um local que você escolher.

2. Para criar um projeto, abra a solução *solução elasticscalestarterkit. sln* do *C#* diretório.

3. Na solução para o projeto de exemplo, abra o arquivo *app. config* . Em seguida, siga as instruções no arquivo para adicionar o nome do servidor do banco de dados SQL do Azure e suas informações de entrada (nome de usuário e senha).

4. Compile e execute o aplicativo. Quando solicitado, habilite o Visual Studio para restaurar os pacotes NuGet da solução. Essa ação baixa a versão mais recente da biblioteca de cliente do banco de dados elástico do NuGet.

5. Para saber mais sobre os recursos da biblioteca de cliente, experimente as várias opções. Observe as etapas que o aplicativo executa na saída do console e fique à vontade para explorar o código nos bastidores.
   
    ![Progresso][4]

Parabéns! Você criou e executou com êxito seu primeiro aplicativo fragmentado usando as ferramentas de banco de dados elástico no banco de dados SQL. Use o Visual Studio ou SQL Server Management Studio para se conectar ao seu banco de dados SQL e dar uma olhada rápida nos fragmentos que o exemplo criou. Você notará novos bancos de dados de fragmentos de exemplo e um banco de dados do Gerenciador de mapa de fragmentos que o exemplo criou.

> [!IMPORTANT]
> É recomendável usar sempre a versão mais recente do Management Studio para que você fique sincronizado com as atualizações do Azure e do banco de dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Partes principais do exemplo de código

* **Gerenciando fragmentos e mapas de fragmentos**: O código ilustra como trabalhar com fragmentos, intervalos e mapeamentos no arquivo *ShardManagementUtils.cs* . Para obter mais informações, consulte [expandir bancos de dados com o Gerenciador de mapa de fragmentos](https://go.microsoft.com/?linkid=9862595).  

* **Roteamento Dependente de dados**: O roteamento de transações para o fragmento correto é mostrado no arquivo *DataDependentRoutingSample.cs* . Para obter mais informações, consulte [Roteamento Dependente de dados](https://go.microsoft.com/?linkid=9862596). 

* **Consultando em vários fragmentos**: A consulta entre fragmentos é ilustrada no arquivo *MultiShardQuerySample.cs* . Para obter mais informações, consulte [consulta de vários fragmentos](https://go.microsoft.com/?linkid=9862597).

* **Adicionando fragmentos vazios**: A adição iterativa de novos fragmentos vazios é executada pelo código no arquivo *CreateShardSample.cs* . Para obter mais informações, consulte [expandir bancos de dados com o Gerenciador de mapa de fragmentos](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Outras operações de escala elástica

* **Dividindo um fragmento existente**: A capacidade de dividir fragmentos é fornecida pela ferramenta de divisão/mesclagem. Para obter mais informações, consulte [movendo dados entre bancos de dado de nuvem expandidos](sql-database-elastic-scale-overview-split-and-merge.md).

* **Mesclando fragmentos existentes**: As mesclagens de fragmento também são executadas usando a ferramenta de divisão/mesclagem. Para obter mais informações, consulte [movendo dados entre bancos de dado de nuvem expandidos](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Custo

A biblioteca de ferramentas de banco de dados elástico é gratuita. Quando você usa ferramentas de banco de dados elástico, não incorre em custos adicionais além do custo do uso do Azure. 

Por exemplo, o aplicativo de exemplo cria novos bancos de dados. O custo desse recurso depende da edição do banco de dados SQL que você escolher e do uso do Azure do seu aplicativo.

Para obter informações sobre preços, consulte [detalhes de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as ferramentas de banco de dados elástico, consulte os seguintes artigos:

* Exemplos de código: 
  * Ferramentas de banco de dados elástico ([.net](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Ferramentas de banco de dados elástico para o SQL do Azure-integração de Entity Framework](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elasticidade de fragmento no script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blogue: [Comunicado de escala elástica](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Canal 9: [Vídeo de visão geral da escala elástica](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum de discussão: [Fórum do banco de dados SQL do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
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

