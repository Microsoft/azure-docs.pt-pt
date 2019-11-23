---
title: Compilar aplicativo Web Java no serviço Linux-Azure App
description: Compile, implante e dimensione aplicativos Web Java do Spring boot com o serviço Azure App no Linux e Azure Cosmos DB.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 077c9b22dbb629c8408d431de3e2e621b79c9c48
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747689"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Tutorial: compilar um aplicativo Web Spring boot do Java com o serviço Azure App no Linux e Azure Cosmos DB

Este tutorial orienta você pelo processo de criação, configuração, implantação e dimensionamento de aplicativos Web Java no Azure. Quando tiver terminado, você terá um aplicativo [Spring boot](https://projects.spring.io/spring-boot/) que armazena dados em [Azure Cosmos DB](/azure/cosmos-db) em execução no [serviço Azure app no Linux](/azure/app-service/containers).

![Aplicativo Spring boot armazenando dados no Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um banco de dados Cosmos DB.
> * Conectar um aplicativo de exemplo ao banco de dados e testá-lo localmente
> * Implantar o aplicativo de exemplo no Azure
> * Transmitir logs de diagnóstico do serviço de aplicativo
> * Adicionar instâncias adicionais para escalar horizontalmente o aplicativo de exemplo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/overview), instalado em seu próprio computador. 
* [Git](https://git-scm.com/)
* [JDK java](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Clonar o aplicativo de TODO o exemplo e preparar o repositório

Este tutorial usa um aplicativo de lista de tarefas de exemplo com uma interface do usuário da Web que chama uma API de Spring REST apoiada pelo [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). O código para o aplicativo está disponível [no GitHub](https://github.com/Microsoft/spring-todo-app). Para saber mais sobre como escrever aplicativos Java usando Spring e Cosmos DB, confira o [iniciador do Spring boot com o tutorial da API do SQL Azure Cosmos DB](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) e os [dados de primavera Azure Cosmos DB início rápido](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Execute os comandos a seguir em seu terminal para clonar o repositório de exemplo e configurar o ambiente de aplicativo de exemplo.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Criar um Azure Cosmos DB

Siga estas etapas para criar um banco de dados Azure Cosmos DB em sua assinatura. O aplicativo de lista de tarefas se conectará a esse banco de dados e armazenará os seus dados durante a execução, mantendo o estado do aplicativo independentemente de onde você executar o aplicativo.

1. Faça logon em seu CLI do Azure e, opcionalmente, defina sua assinatura se você tiver mais de um conectado às suas credenciais de logon.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Crie um grupo de recursos do Azure, observando o nome do grupo de recursos.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Crie Azure Cosmos DB com o tipo de `GlobalDocumentDB`. O nome de Cosmos DB deve usar apenas letras minúsculas. Anote o campo `documentEndpoint` na resposta do comando.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Obtenha sua chave de Azure Cosmos DB para se conectar ao aplicativo. Mantenha o `primaryMasterKey``documentEndpoint` próximo, pois você precisará deles na próxima etapa.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Configurar as propriedades do aplicativo de tarefas pendentes

Abra um terminal no seu computador. Copie o arquivo de script de exemplo no repositório clonado para que você possa personalizá-lo para o banco de dados Cosmos DB que acabou de criar.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Edite `.scripts/set-env-variables.sh` em seu editor favorito e forneça Azure Cosmos DB informações de conexão. Para a configuração do Linux do serviço de aplicativo, use a mesma região que antes (`your-resource-group-region`) e o grupo de recursos (`your-azure-group-name`) usados ao criar o banco de dados do Cosmos DB. Escolha um WEBAPP_NAME exclusivo, pois ele não pode duplicar nenhum nome de aplicativo Web em nenhuma implantação do Azure.

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
   
Essas variáveis de ambiente são usadas em `application.properties` no aplicativo de lista de tarefas. Os campos no arquivo de propriedades definem uma configuração de repositório padrão para Spring Data:

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

Em seguida, o aplicativo de exemplo usa a anotação `@Document` importada do `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` para configurar um tipo de entidade a ser armazenado e gerenciado pelo Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Use o Maven para executar o exemplo.

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

Você pode acessar o aplicativo de TODO o Spring localmente usando este link depois que o aplicativo for iniciado: [http://localhost:8080/](http://localhost:8080/).

 ![Acessar o aplicativo Spring TODO localmente](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Se você vir exceções em vez da mensagem "iniciado TodoApplication", verifique se o script `bash` na etapa anterior exportou as variáveis de ambiente corretamente e se os valores estão corretos para o banco de dados de Azure Cosmos DB que você criou.

## <a name="configure-azure-deployment"></a>Configurar a implementação do Azure

Abra o arquivo `pom.xml` no diretório `initial/spring-boot-todo` e adicione o seguinte [plug-in do Maven para Azure app configuração do serviço](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) .

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.8.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
             </runtime>
             <deployment>
                 <resources>
                 <resource>
                     <directory>${project.basedir}/target</directory>
                     <includes>
                     <include>*.jar</include>
                     </includes>
                 </resource>
                 </resources>
             </deployment>

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

## <a name="deploy-to-app-service-on-linux"></a>Implantar no serviço de aplicativo no Linux

Use a meta `azure-webapp:deploy` Maven para implantar o aplicativo de tarefas pendentes no serviço Azure App no Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.8.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

A saída contém a URL para seu aplicativo implantado (neste exemplo, `https://spring-todo-app.azurewebsites.net`). Você pode copiar essa URL no navegador da Web ou executar o comando a seguir na janela do seu terminal para carregar seu aplicativo.

```bash
open https://spring-todo-app.azurewebsites.net
```

Você deve ver o aplicativo em execução com a URL remota na barra de endereços:

 ![Aplicativo Spring boot em execução com uma URL remota](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>Escalar horizontalmente o aplicativo TODO

Escalar horizontalmente o aplicativo adicionando outro trabalhador:

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

## <a name="next-steps"></a>Passos seguintes

O [Azure para desenvolvedores Java](/java/azure/)
[Spring boot](https://spring.io/projects/spring-boot), [spring data para Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) e [serviço de aplicativo Linux](app-service-linux-intro.md).

Saiba mais sobre como executar aplicativos Java no serviço de aplicativo no Linux no guia do desenvolvedor.

> [!div class="nextstepaction"] 
> [Java no guia de desenvolvimento do Linux do serviço de aplicativo](configure-language-java.md)
