---
title: 'Tutorial: Aplicativo Linux Java com MongoDB'
description: Saiba como obter uma aplicação Linux Java orientada por dados a funcionar no Azure App Service, com ligação a um MongoDB em execução em Azure (Cosmos DB).
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7a0dd1198bcad675f7662a2cf4eb369f2a276445
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479411"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Tutorial: Construa uma aplicação web Java Spring Boot com serviço de aplicações Azure em Linux e Azure Cosmos DB

Este tutorial acompanha-o através do processo de construção, configuração, implantação e escalonamento de aplicações web java em Azure. Quando terminar, terá uma aplicação [Spring Boot](https://projects.spring.io/spring-boot/) armazenando dados em [Azure Cosmos DB](../cosmos-db/index.yml) em execução no [Azure App Service em Linux](overview.md).

![Aplicação Boot de mola armazenando dados em Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma base de dados cosmos DB.
> * Conecte uma aplicação de amostra à base de dados e teste-a localmente
> * Implementar a aplicação da amostra para Azure
> * Fluxo de registos de diagnóstico do Serviço de Aplicações
> * Adicione instâncias adicionais para escalar a aplicação da amostra

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Azure CLI,](/cli/azure/overview)instalado no seu próprio computador. 
* [Git](https://git-scm.com/)
* [Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Clone a amostra toda a app e prepare o repo

Este tutorial usa uma aplicação de lista de amostras TODO com um UI web que chama uma API de Rest de primavera apoiada por [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). O código da aplicação está disponível [no GitHub.](https://github.com/Microsoft/spring-todo-app) Para saber mais sobre a escrita de aplicativos Java usando Spring and Cosmos DB, consulte o [Starter de Botas de primavera com o tutorial AZure Cosmos DB SQL API](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) e o início rápido do Spring Data [Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Execute os seguintes comandos no seu terminal para clonar a amostra de repo e configurar o ambiente da aplicação da amostra.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Criar uma base de dados do Azure Cosmos DB

Siga estes passos para criar uma base de dados DB Azure Cosmos na sua subscrição. A aplicação toda a lista ligar-se-á a esta base de dados e armazenará os seus dados ao executar, persistindo o estado da aplicação independentemente do local onde executa a aplicação.

1. Faça login no seu Azure CLI e desabrade opcionalmente a sua subscrição se tiver mais de uma ligada às suas credenciais de login.

    ```azurecli
    az login
    az account set -s <your-subscription-id>
    ```   

2. Crie um Grupo de Recursos Azure, observando o nome do grupo de recursos.

    ```azurecli
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Crie Azure Cosmos DB com o `GlobalDocumentDB` tipo. O nome de Cosmos DB deve usar apenas letras minúsculas. Note no `documentEndpoint` campo na resposta do comando.

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Obtenha a sua chave DB Azure Cosmos para ligar à aplicação. Mantenha o `primaryMasterKey` , por perto como vai precisar deles no próximo `documentEndpoint` passo.

    ```azurecli
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Configure as propriedades da aplicação TODO

Abra um terminal no computador. Copie o ficheiro de script de amostra no repo clonado para que possa personalizá-lo para a sua base de dados Cosmos DB que acabou de criar.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Edite `.scripts/set-env-variables.sh` no seu editor favorito e forneça informações de conexão DB da Azure Cosmos. Para a configuração do Serviço de Aplicações Linux, utilize a mesma região que antes ( `your-resource-group-region` ) e grupo de recursos ( ) `your-azure-group-name` utilizados na criação da base de dados Cosmos DB. Escolha um WEBAPP_NAME único, uma vez que não pode duplicar qualquer nome de aplicação web em qualquer implementação do Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Em seguida, executar o script:

```bash
source .scripts/set-env-variables.sh
```
   
Estas variáveis ambientais são utilizadas `application.properties` na aplicação toda a lista. Os campos no ficheiro de propriedades configuram uma configuração de repositório padrão para dados de mola:

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

Em seguida, a aplicação da amostra utiliza a `@Document` anotação importada `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` para configurar um tipo de entidade a ser armazenado e gerido pela Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Usa o Maven para analisar a amostra.

```bash
mvn package spring-boot:run
```

A saída deve parecer-se com a seguinte.

```output
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

Pode aceder à App Spring TODO local usando este link assim que a aplicação for `http://localhost:8080/` iniciada:

 ![Access Spring TODO app localmente](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Se vir exceções em vez da mensagem "Started TodoApplication", verifique se o `bash` script na etapa anterior exportou corretamente as variáveis ambientais e que os valores estão corretos para a base de dados DB do Azure Cosmos que criou.

## <a name="configure-azure-deployment"></a>Configurar a implementação do Azure

Abra o `pom.xml` ficheiro no `initial/spring-boot-todo` diretório e adicione o seguinte  [Plugin de Aplicação Web Azure para](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) configuração Maven.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.13.0</version>
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

## <a name="deploy-to-app-service-on-linux"></a>Implementar para o Serviço de Aplicações em Linux

Utilize o `mvn azure-webapp:deploy` objetivo maven para implementar a app TODO para o Azure App Service em Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.11.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Auth Type : AZURE_CLI, Auth Files : [C:\Users\testuser\.azure\azureProfile.json, C:\Users\testuser\.azure\accessTokens.json]
[INFO] Subscription : xxxxxxxxx
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

A saída contém o URL para a sua aplicação implementada (neste exemplo, `https://spring-todo-app.azurewebsites.net` ). Pode copiar este URL no seu navegador web ou executar o seguinte comando na janela do Terminal para carregar a sua aplicação.

```bash
explorer https://spring-todo-app.azurewebsites.net
```

Deve ver a aplicação a funcionar com o URL remoto na barra de endereços:

 ![Aplicação boot de mola correndo com um URL remoto](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>Escale a App TODO

Escale a aplicação adicionando outro trabalhador:

```azurecli
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar destes recursos para outro tutorial (ver [próximos passos),](#next)pode eliminá-los executando o seguinte comando na Cloud Shell:Â€ 
```azurecli
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

[Azure para Desenvolvedores](/java/azure/) 
 de Java [Boot de primavera](https://spring.io/projects/spring-boot), [dados de primavera para Cosmos DB,](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) [Azure Cosmos DB](../cosmos-db/introduction.md) e [App Service Linux](overview.md).

Saiba mais sobre a execução de aplicativos Java no Serviço de Aplicações em Linux no guia do desenvolvedor.

> [!div class="nextstepaction"] 
> [Guia para programadores de Java no Serviço de Aplicações do Linux](configure-language-java.md?pivots=platform-linux)
