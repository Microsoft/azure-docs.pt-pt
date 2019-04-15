---
title: Criar aplicação web Java no Linux - serviço de aplicações do Azure
description: Criar, implementar e dimensionar aplicações Web de Java Spring Boot com o serviço de aplicações do Azure no Linux e o Azure Cosmos DB.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: f86949c196507080b32771a1b5470e9911e3e5b7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545789"
---
# <a name="tutorial-build-a-java-web-app-using-spring-and-azure-cosmos-db"></a>Tutorial: Criar uma aplicação de web de Java com o Spring e o Azure Cosmos DB

Este tutorial explica-lhe o processo de criar, configurar, implementar e dimensionar aplicações web em Java no Azure. Quando tiver terminado, terá uma [Spring Boot](https://projects.spring.io/spring-boot/) aplicação armazena os dados na [do Azure Cosmos DB](/azure/cosmos-db) em execução no [serviço de aplicações do Azure no Linux](/azure/app-service/containers).

![Aplicação Java em execução no serviço de aplicações do Azure](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma base de dados do Cosmos DB.
> * Ligar uma aplicação de exemplo para a base de dados e testá-lo localmente
> * Implementar a aplicação de exemplo no Azure
> * Registos de diagnóstico do Stream do serviço de aplicações
> * Adicionar mais instâncias para aumentar horizontalmente a aplicação de exemplo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/overview), instalado no seu computador. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Clone a aplicação de lista de tarefas de exemplo e preparar o repositório

Este tutorial utiliza uma aplicação de lista de tarefas de exemplo com uma web da interface do Usuário que chama uma API de REST de Spring alicerçado [Spring dados do Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). O código para a aplicação está disponível [no GitHub](https://github.com/Microsoft/spring-todo-app). Para saber mais sobre como escrever aplicações de Java com o Spring e o Cosmos DB, veja a [Spring Boot Starter com o tutorial do Azure Cosmos DB SQL API](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) e o [início rápido do Spring dados do Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Execute os seguintes comandos no seu terminal para clonar o repositório de exemplo e configurar o ambiente de aplicação de exemplo.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Criar um Azure Cosmos DB

Siga estes passos para criar uma base de dados do Azure Cosmos DB na sua subscrição. A aplicação de lista de tarefas irá ligar a esta base de dados e armazenar os dados quando em execução, manter o estado do aplicativo, independentemente de onde o aplicativo é executado.

1. Início de sessão a CLI do Azure e se desejar, defina a sua subscrição, se tiver mais do que uma ligada para as suas credenciais de início de sessão.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Crie um grupo de recursos do Azure, observar o nome do grupo de recursos.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Criar o Azure Cosmos DB com o `GlobalDocumentDB` tipo. O nome do Cosmos DB tem de utilizar apenas as letras minúsculas. Tome nota do `documentEndpoint` campo na resposta do comando.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Obtenha a chave do Azure Cosmos DB para ligar à aplicação. Manter o he `primaryMasterKey`, `documentEndpoint` nearby, pois precisará-los no próximo passo.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Configurar as propriedades da aplicação de lista de tarefas

Abra um terminal no seu computador. Copie o ficheiro de script de exemplo no repositório clonado para que pode personalizá-lo para a base de dados do Cosmos DB que acabou de criar.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Editar `.scripts/set-env-variables.sh` no seu editor favorito e fornecimento de informações de ligação do Azure Cosmos DB. Para a configuração do Linux do serviço de aplicações, utilizar a mesma região como antes (`your-resource-group-region`) e o grupo de recursos (`your-azure-group-name`) utilizadas ao criar a base de dados do Cosmos DB. Escolha um WEBAPP_NAME que seja exclusivo, uma vez que ele não é possível duplicar qualquer nome da aplicação web em qualquer implementação do Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Em seguida, execute o script:

```bash
source .scripts/set-env-variables.sh
```
   
Estas variáveis de ambiente são utilizadas no `application.properties` na aplicação de lista de tarefas. Os campos no ficheiro de propriedades, configure uma configuração predefinida do repositório para dados de Spring:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

Em seguida, a aplicação de exemplo utiliza a `@Document` importados de anotação de `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` para configurar um tipo de entidade a ser armazenadas e geridas pelo Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Utilize Maven para executar o exemplo.

```bash
mvn package spring-boot:run
```

O resultado deverá ser semelhante ao seguinte.

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Pode aceder a aplicação de tarefas de Spring localmente utilizando esta ligação depois de iniciada a aplicação: [ http://localhost:8080/ ](http://localhost:8080/).

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Se vir exceções em vez da mensagem "TodoApplication iniciado", verifique se o `bash` script no passo anterior exportado as variáveis de ambiente corretamente e que os valores estão corretos para a base de dados do Azure Cosmos DB que criou.

## <a name="configure-azure-deployment"></a>Configurar a implementação do Azure

Abra o `pom.xml` de ficheiros a `initial/spring-boot-todo` directory e adicione o seguinte [Plug-in do Maven para serviço de aplicações do Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) configuração.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Implementar o serviço de aplicações no Linux

Utilize o `azure-webapp:deploy` objetivo do Maven para implementar a aplicação de lista de tarefas para o serviço de aplicações do Azure no Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

A saída contém o URL para a aplicação implementada (neste exemplo, `https://spring-todo-app.azurewebsites.net` ). Pode copiar esta URL no seu navegador da web ou execute o seguinte comando na janela de Terminal para carregar a sua aplicação.

```bash
open https://spring-todo-app.azurewebsites.net
```

Deverá ver a aplicação em execução com o URL na barra de endereço remoto:

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>Aumentar horizontalmente a aplicação de lista de tarefas

Aumentar horizontalmente a aplicação através da adição de outro worker:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar destes recursos para outro tutorial (veja os [Passos seguintes](#next)), pode eliminá-los ao executar o seguinte comando no Cloud Shell: 
  
```bash
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Passos Seguintes

[Azure para programadores Java](/java/azure/)
[da Primavera de arranque](https://spring.io/projects/spring-boot), [da Primavera de dados para Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) e [Linux do serviço de aplicações ](app-service-linux-intro.md).

Saiba mais sobre como executar aplicações Java no serviço de aplicações no Linux no Guia do programador.

> [!div class="nextstepaction"] 
> [Java no guia de desenvolvimento do Linux do serviço de aplicações](configure-language-java.md)
