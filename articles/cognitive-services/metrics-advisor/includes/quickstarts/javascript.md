---
title: Métricas Advisor JavaScript quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: a4426e9ca40b21c79e5e34f782be4ff2d07c7061
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186878"
---
[Documentação de referência](https://docs.microsoft.com/javascript/api/overview/azure/ai-metrics-advisor-readme-pre?view=azure-node-preview&preserve-view=true)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor)  |  [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual de [Node.js](https://nodejs.org/)
* Assim que tiver a sua subscrição Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Crie um recurso Metrics Advisor crie um "  target="_blank"> recurso Metrics Advisor no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para implementar a sua instância Metrics Advisor.  
* A sua própria base de dados SQL com dados de séries de tempo.
  
> [!TIP]
> * Pode encontrar amostras do JavaScript Metrics Advisor no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples).
> * Pode levar 10 a 30 minutos para o seu recurso Metrics Advisor implementar uma instância de serviço para que possa utilizar. Clique **em Ir para o recurso** assim que implementar com sucesso. Após a implementação, pode começar a utilizar a sua instância Metrics Advisor com o portal web e a API REST. 
> * Pode encontrar o URL para a API REST no portal Azure, na secção **Visão Geral** do seu recurso. Terá o seguinte aspeto:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Instale o `@azure/ai-metrics-advisor` pacote NPM:

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.1
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

Criar um ficheiro com o nome `index.js` e importar as seguintes bibliotecas:

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript)que contém os exemplos de código neste arranque rápido.

Crie variáveis para o ponto final e chave Azure do seu recurso. 

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso Metrics Advisor que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes** . Pode encontrar as chaves de subscrição e o ponto final na página **Chave e Ponta final** do recurso, na **Gestão de Recursos.** <br><br>Para recuperar a sua chave API tem de ir a [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Selecione o apropriado: **Diretório,** **Subscrições** e **Espaço de Trabalho** para o seu recurso e escolha Começar a **trabalhar** . Poderá então recuperar as suas chaves API de [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) dos Serviços Cognitivos para obter mais informações.

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais funcionalidades do Advisor Métricas JavaScript SDK.

|Nome|Descrição|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/classes/metricsadvisorclient.html) | **Usado para:** <br> - Listagem de incidentes <br> - Listagem da causa principal dos incidentes <br> - Recuperação dos dados originais das séries de tempo e dos dados das séries de tempo enriquecidos pelo serviço. <br> - Alertas de listagem <br> - Adicionar feedback para sintonizar o seu modelo |
| [MétricasAdvisorDissinsecimento](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/classes/metricsadvisoradministrationclient.html)| **Permite-lhe:** <br> - Gerir feeds de dados <br> - Criar, configurar, recuperar, listar e eliminar configurações de alerta de anomalias <br> - Gerir ganchos  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/interfaces/datafeed.html)| **O que o Metrics Advisor ingere a partir da sua fonte de dados. A `DataFeed` contém linhas de:** <br> - Prazos <br> - Dimensões zero ou mais <br> - Uma ou mais medidas  |
| [Métrica](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-metrics-advisor/1.0.0-beta.1/interfaces/metric.html) | A `Metric` é uma medida quantificável que é usada para monitorizar e avaliar o estado de um processo de negócio específico. Pode ser uma combinação de múltiplos valores de séries de tempo divididos em dimensões. Por exemplo, uma métrica de saúde web pode conter dimensões para a contagem de utilizadores e para o mercado en-us. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Metrics Advisor para JavaScript:

* [Autenticar o cliente](#authenticate-the-client)
* [Adicionar um feed de dados](#add-a-data-feed)
* [Verifique o estado da ingestão](#check-ingestion-status)
* [Deteção de anomalias de configuração](#configure-anomaly-detection)
* [Criar um gancho](#create-a-hook)
* [Criar uma configuração de alerta](#create-an-alert-configuration)
* [Consultar o alerta](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Assim que tiver as duas chaves e o endereço de ponto final, pode utilizar a classe MetricsAdvisorKeyCredential para autenticar os clientes da seguinte forma:

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>Adicionar um feed de dados

O Metrics Advisor suporta a ligação de diferentes tipos de fontes de dados. Aqui está uma amostra para ingerir dados do SQL Server.

`sql_server_connection_string`Substitua-o pela sua própria cadeia de ligação ao servidor SQL e substitua-os `query` por uma consulta que retorne os seus dados a um único tempo. Também terá de ajustar os `metric` valores e `dimension` valores com base nos seus dados personalizados.

> [!IMPORTANT]
> A consulta deve voltar no máximo um registo para cada combinação de dimensão, em cada hora de temperatura. E todos os registos devolvidos pela consulta devem ter os mesmos tempos. O Metrics Advisor executará esta consulta para cada timetamp para ingerir os seus dados. Consulte a [secção faq em consultas](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) para obter mais informações e exemplos. 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  const metric = [
    {
      name: "revenue",
      displayName: "revenue",
      description: "Metric1 description"
    },
    {
      name: "cost",
      displayName: "cost",
      description: "Metric2 description"
    }
  ];
  const dimension = [
    { name: "city", displayName: "city display" },
    { name: "category", displayName: "category display" }
  ];
  const dataFeedSchema = {
    metrics: metric,
    dimensions: dimension,
    timestampColumn: null
  };
  const dataFeedIngestion = {
    ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
    ingestionStartOffsetInSeconds: 0,
    dataSourceRequestConcurrency: -1,
    ingestionRetryDelayInSeconds: -1,
    stopRetryAfterInSeconds: -1
  };
  const granualarity = {
    granularityType: "Daily"
  };
  const source = {
    dataSourceType: "SqlServer",
    dataSourceParameter: {
      connectionString: sqlServerConnectionString,
      query: sqlServerQuery
    }
  };
  const options = {
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    admins: ["xyz@example.com"]
  };

  console.log("Creating Datafeed...");
  const result = await adminClient.createDataFeed({
    name: "test_datafeed_" + new Date().getTime().toFixed(),
    source,
    granularity,
    schema: dataFeedSchema,
    ingestionSettings: dataFeedIngestion,
    options
  });

  return result;
}
```

## <a name="check-ingestion-status"></a>Verifique o estado da ingestão

Depois de começarmos a ingestão de dados, podemos verificar o estado da ingestão.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  for await (const status of adminClient.listDataFeedIngestionStatus(
    datafeedId,
    startTime,
    endTime
  )) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>Deteção de anomalias de configuração

Precisamos de uma configuração de deteção de anomalias para determinar se um ponto na série de tempo é uma anomalia. Enquanto uma configuração de deteção predefinida é aplicada automaticamente a cada métrica, pode sintonizar os modos de deteção utilizados nos seus dados criando uma configuração de deteção de anomalias personalizada.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  return await adminClient.createMetricAnomalyDetectionConfiguration({
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  });
}
```

### <a name="create-a-hook"></a>Criar um gancho

Usamos ganchos para subscrever alertas em tempo real. Neste exemplo, criamos um webhook para o serviço Metrics Advisor para postar o alerta para.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts",
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>Criar uma configuração de alerta

Esta amostra irá mostrar-lhe como configurar as condições que um alerta precisa de ser desencadeado e quais os ganchos a usar como destino para um alerta.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const metricAlertingConfig = {
    detectionConfigurationId: detectionConfigId,
    alertScope: {
      scopeType: "All"
    },
    alertConditions: {
      severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
    },
    snoozeCondition: {
      autoSnooze: 0,
      snoozeScope: "Metric",
      onlyForSuccessive: true
    }
  };
  return await adminClient.createAnomalyAlertConfiguration({
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [metricAlertingConfig],
    hookIds,
    description: "Alerting config description"
  });
}
```

## <a name="query-the-alert"></a>Consultar o alerta

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alertIds = [];
  for await (const alert of client.listAlertsForAlertConfiguration(
    alertConfigId,
    startTime,
    endTime,
    "AnomalyTime"
  )) {
    alertIds.push(alert.id);
  }

  return alertIds;
}

async function queryAnomaliesByAlert(client, alertConfigId, alertId) {
  console.log(
    `Listing anomalies for alert configuration '${alertConfigId}' and alert '${alertId}'`
  );
  for await (const anomaly of client.listAnomaliesForAlert(alertConfigId, alertId)) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```