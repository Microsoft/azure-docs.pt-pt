---
title: 'Tutorial: Use funções java com Azure Cosmos DB e Centros de Eventos'
description: Este tutorial mostra-lhe como consumir eventos a partir de Event Hubs para fazer atualizações em Azure Cosmos DB usando uma função escrita em Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: b6d7b2c60e777266b1cab578b8970c1fa1c6bc50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77425328"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Tutorial: Criar uma função em Java com um gatilho do Event Hub e uma ligação de saída Azure Cosmos DB

Este tutorial mostra-lhe como usar funções Azure para criar uma função Java que analisa um fluxo contínuo de dados de temperatura e pressão. Eventos de hub de eventos que representam leituras de sensores desencadeiam a função. A função processa os dados do evento, em seguida, adiciona entradas de estado a um Azure Cosmos DB.

Neste tutorial, vai:

> [!div class="checklist"]
> * Crie e configure os recursos Azure utilizando o Azure CLI.
> * Crie e teste as funções java que interagem com estes recursos.
> * Desloque as suas funções para o Azure e monitorize-as com insights de aplicação.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, deve ter o seguinte instalado:

* [Java Development Kit](https://aka.ms/azure-jdks), versão 8
* [Apache Maven](https://maven.apache.org), versão 3.0 ou superior
* [Azure CLI](/cli/azure/install-azure-cli) se preferir não usar Cloud Shell
* [Funções Azure Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) versão 2.6.666 ou superior

> [!IMPORTANT]
> A `JAVA_HOME` variável ambiental deve ser definida para a localização de instalação do JDK para completar este tutorial.

Se preferir utilizar o código para este tutorial diretamente, consulte o repo da amostra [java-functions-eventhub-cosmosdb.](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Criar recursos do Azure

Neste tutorial, você precisará destes recursos:

* Um grupo de recursos para conter os outros recursos
* Um espaço de nome de Hubs de Eventos, centro de eventos e regra de autorização
* Uma conta Cosmos DB, base de dados e recolha
* Uma aplicação de função e uma conta de armazenamento para hospedar

As seguintes secções mostram-lhe como criar estes recursos utilizando o Azure CLI.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Se não estiver a usar a Cloud Shell, terá de utilizar o Azure CLI localmente para aceder à sua conta. Utilize `az login` o comando da solicitação bash para lançar a experiência de login baseada no navegador. Se tiver acesso a mais de uma subscrição `az account set --subscription` Azure, detete o padrão com o seguinte pelo ID de subscrição.

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Em seguida, crie algumas variáveis ambientais para os nomes e localização dos recursos que irá criar. Utilize os seguintes comandos, substituindo os `<value>` espaços reservados por valores à sua escolha. Os valores devem estar em conformidade com as [regras de nomeação e restrições aos recursos do Azure.](/azure/architecture/best-practices/resource-naming) Para `LOCATION` a variável, utilize um dos `az functionapp list-consumption-locations` valores produzidos pelo comando.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

O resto deste tutorial usa estas variáveis. Esteja ciente de que estas variáveis persistem apenas durante a duração da sua atual sessão Azure CLI ou Cloud Shell. Terá de executar estes comandos novamente se utilizar uma janela de terminal local diferente ou se a sua sessão cloud Shell passar.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure utiliza grupos de recursos para recolher todos os recursos relacionados na sua conta. Assim, pode vê-los como uma unidade e eliminá-los com um único comando quando terminar com eles.

Utilize o seguinte comando para criar um grupo de recursos:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Em seguida, crie um espaço de nome sinuoso, centro de eventos e regra de autorização do Azure Event Hubs utilizando os seguintes comandos:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

O espaço de nome do Event Hubs contém o verdadeiro centro de eventos e a sua regra de autorização. A regra de autorização permite que as suas funções enviem mensagens para o centro e ouçam os eventos correspondentes. Uma função envia mensagens que representam dados de telemetria. Outra função ouve eventos, analisa os dados do evento e armazena os resultados em Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Criar uma base de dados do Azure Cosmos DB

Em seguida, crie uma conta Azure Cosmos DB, base de dados e recolha utilizando os seguintes comandos:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

O `partition-key-path` valor partilha os seus `temperatureStatus` dados com base no valor de cada item. A chave de partição permite à Cosmos DB aumentar o desempenho dividindo os seus dados em subconjuntos distintos a que pode aceder de forma independente.

### <a name="create-a-storage-account-and-function-app"></a>Criar uma conta de armazenamento e app de função

Em seguida, crie uma conta de Armazenamento Azure, que é exigida pelas Funções Azure, e depois crie a aplicação de função. Utilize os seguintes comandos:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

Quando `az functionapp create` o comando cria a sua aplicação de função, também cria um recurso Deinsights de Aplicação com o mesmo nome. A aplicação de funções é `APPINSIGHTS_INSTRUMENTATIONKEY` configurada automaticamente com uma definição chamada que a liga aos Insights de Aplicação. Pode ver a telemetria de aplicações depois de implementar as suas funções no Azure, como descrito mais tarde neste tutorial.

## <a name="configure-your-function-app"></a>Configure a sua aplicação de função

A sua aplicação de funções terá de aceder aos outros recursos para funcionar corretamente. As seguintes secções mostram-lhe como configurar a sua aplicação de funções para que possa funcionar na sua máquina local.

### <a name="retrieve-resource-connection-strings"></a>Recuperar cordas de ligação de recursos

Utilize os seguintes comandos para recuperar as cordas de armazenamento, centro de eventos e cordas de ligação Cosmos DB e guarde-as em variáveis ambientais:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Estas variáveis são definidas para valores recuperados dos comandos Azure CLI. Cada comando utiliza uma consulta JMESPath para extrair a cadeia de ligação da carga útil jSON devolvida. As cordas de ligação `echo` também são exibidas utilizando para que possa confirmar que foram recuperadas com sucesso.

### <a name="update-your-function-app-settings"></a>Atualize as definições da sua aplicação de função

Em seguida, utilize o seguinte comando para transferir os valores de cadeia de ligação para as definições de aplicações na sua conta Funções Azure:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Os seus recursos Azure foram agora criados e configurados para trabalhar em conjunto.

## <a name="create-and-test-your-functions"></a>Crie e teste as suas funções

Em seguida, você vai criar um projeto na sua máquina local, adicionar código Java, e testá-lo. Utilizará comandos que funcionem com o Plugin funções Azure para Maven e as Ferramentas Core funções Azure. As suas funções serão executadas localmente, mas usarão os recursos baseados na nuvem que criou. Depois de ter as funções a funcionar localmente, pode usar a Maven para as implementar na nuvem e ver os seus dados e análises acumularem-se.

Se usou a Cloud Shell para criar os seus recursos, então não estará ligado ao Azure localmente. Neste caso, utilize `az login` o comando para lançar o processo de login baseado no navegador. Em seguida, se necessário, `az account set --subscription` detete a subscrição predefinida com o seguinte o ID de subscrição. Por fim, execute os seguintes comandos para recriar algumas variáveis ambientais na sua máquina local. Substitua `<value>` os espaços reservados com os mesmos valores utilizados anteriormente.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Criar um projeto de funções locais

Utilize o seguinte comando Maven para criar um projeto de funções e adicionar as dependências necessárias.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Este comando gera vários `telemetry-functions` ficheiros dentro de uma pasta:

* Um `pom.xml` ficheiro para uso com Maven
* Um `local.settings.json` ficheiro para manter as definições de aplicativos para testes locais
* Um `host.json` ficheiro que permite o Pacote de Extensão de Funções Azure, necessário para a ligação de saída do Cosmos DB na sua função de análise de dados
* Um `Function.java` ficheiro que inclui uma implementação de função predefinida
* Alguns ficheiros de teste que este tutorial não precisa

Para evitar erros de compilação, terá de eliminar os ficheiros de teste. Executar os seguintes comandos para navegar para a nova pasta do projeto e eliminar a pasta de teste:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Recupere as definições da sua aplicação de função para uso local

Para testes locais, o seu projeto de função necessitará das cordas de ligação que adicionou à sua aplicação de funções em Azure no início deste tutorial. Utilize o seguinte comando de Ferramentas Core Funções Azure, que recupera todas as `local.settings.json` definições da aplicação de função armazenadas na nuvem e as adiciona ao seu ficheiro:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Adicione código Java

Em seguida, `Function.java` abra o ficheiro e substitua o conteúdo pelo seguinte código.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Como pode ver, este ficheiro contém `generateSensorData` `processSensorData`duas funções, e . A `generateSensorData` função simula um sensor que envia leituras de temperatura e pressão para o centro do evento. Um gatilho temporizador executa a função a cada 10 segundos, e uma ligação de saída do hub de evento envia o valor de retorno para o centro do evento.

Quando o centro do evento recebe a mensagem, gera um evento. A `processSensorData` função funciona quando recebe o evento. Em seguida, processa os dados do evento e usa uma ligação de saída Do Azure Cosmos DB para enviar os resultados para o Azure Cosmos DB.

Os dados utilizados por estas funções `TelemetryItem`são armazenados utilizando uma classe chamada , que terá de implementar. Criar um novo `TelemetryItem.java` ficheiro chamado `Function.java` no mesmo local que e adicionar o seguinte código:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Executar localmente

Agora pode construir e executar as funções localmente e ver dados aparecerem no seu Azure Cosmos DB.

Utilize os seguintes comandos Maven para construir e executar as funções:

```bash
mvn clean package
mvn azure-functions:run
```

Depois de algumas mensagens de construção e arranque, verá a saída semelhante ao seguinte exemplo para cada vez que as funções funcionam:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Em seguida, pode ir ao [portal Azure](https://portal.azure.com) e navegar para a sua conta Azure Cosmos DB. Selecione **Data Explorer,** expanda **o TelemettryInfo**e, em seguida, selecione **Itens** para ver os seus dados quando chegar.

![Cosmos DB Data Explorer](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Desloque para Azure e veja a telemetria da aplicação

Por fim, pode implementar a sua app para o Azure e verificar se continua a funcionar da mesma forma que fez localmente.

Implemente o seu projeto para o Azure utilizando o seguinte comando:

```bash
mvn azure-functions:deploy
```

As suas funções funcionam agora em Azure e continuam a acumular dados no seu Azure Cosmos DB. Pode ver a sua aplicação de função implantada no portal Azure e visualizar a telemetria das aplicações através do recurso Desinformação de Aplicações conectado, como mostram as seguintes imagens:

**Fluxo de Métricas Ao Vivo:**

![Fluxo de métricas ao vivo insights de aplicação](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Desempenho:**

![Insights de aplicação Lâmina de desempenho](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos do Azure criados neste tutorial, pode utilizar o comando abaixo para os eliminar:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma Função Azure que lida com eventos do Event Hub e atualiza um Cosmos DB. Para mais informações, consulte o guia de desenvolvimento das [funções Azure Java](/azure/azure-functions/functions-reference-java). Para obter informações sobre as anotações utilizadas, consulte a referência [com.microsoft.azure.functions.anotation.](/java/api/com.microsoft.azure.functions.annotation)

Este tutorial utilizou variáveis ambientais e configurações de aplicação para armazenar segredos como cordas de ligação. Para obter informações sobre o armazenamento destes segredos no Cofre de Chaves Azure, consulte as referências do Cofre de Chaves para o Serviço de [Aplicações e funções Azure](/azure/app-service/app-service-key-vault-references).

Em seguida, aprenda a utilizar o Ci/CD dos Gasodutos Azure para a implantação automatizada:

> [!div class="nextstepaction"]
> [Construir e implantar Java para funções azure](/azure/devops/pipelines/ecosystems/java-function)
