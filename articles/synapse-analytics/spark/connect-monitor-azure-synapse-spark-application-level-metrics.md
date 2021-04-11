---
title: Tutorial - Ligar e monitorizar as métricas do nível de aplicação de faíscas Azure Synapse
description: Tutorial - Aprenda a integrar o servidor Prometheus existente no local com o espaço de trabalho Azure Synapse para métricas de aplicação Azure Spark em tempo real utilizando o conector Synapse Prometheus.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: d22975199eedae353f2dc12588671ae4b54c85ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109323"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Tutorial: Ligar e monitorizar as métricas do nível de aplicação de faíscas Azure Synapse

## <a name="overview"></a>Descrição Geral

Neste tutorial, você aprenderá a integrar o seu servidor Prometheus existente no local com espaço de trabalho Azure Synapse para métricas de aplicação Azure Spark em tempo real usando o conector Synapse Prometheus. 

Este tutorial também introduz as métricas Azure Synapse REST APIs. Pode obter dados de métricas de aplicação Spark através das APIs REST para construir o seu próprio kit de ferramentas de monitorização e diagnóstico ou integrar-se com os seus sistemas de monitorização.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Utilize o conector Azure Synapse Prometheus para os seus servidores Prometheus no local

[O conector Azure Synapse Prometheus](https://github.com/microsoft/azure-synapse-spark-metrics) é um projeto de código aberto. O conector Synapse Prometheus utiliza um método de descoberta de serviço baseado em ficheiros para permitir:
 - Autenticar para o espaço de trabalho synapse através de um diretor de serviço AAD.
 - Pegue a lista de aplicações apache Spark do espaço de trabalho. 
 - Puxe as métricas da aplicação Spark através da configuração baseada em ficheiros Prometheus. 

### <a name="1-prerequisite"></a>1. Pré-requisito

Precisa de ter um servidor Prometheus implantado num VM Linux.

### <a name="2-create-a-service-principal"></a>2. Criar um diretor de serviço

Para utilizar o conector Azure Synapse Prometheus no seu servidor Prometheus no local, deve seguir os passos abaixo para criar um principal de serviço.

#### <a name="21-create-a-service-principal"></a>2.1 Criar um principal de serviço:

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

O resultado deve parecer:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Note a appId, senha e identificação do inquilino.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2.2 Adicione as permissões correspondentes ao principal de serviço criado no degrau acima.

![autorização de concessão de screenshot srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Inicie sessão no seu [espaço de trabalho Azure Synapse Analytics](https://web.azuresynapse.net/) como Administrador da Sinapse

2. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir > controlo de acesso**

3. Clique no botão Adicionar na parte superior esquerda para **adicionar uma atribuição de função**

4. Para âmbito, escolha **espaço de trabalho**

5. Para função, escolha **o Operador de Computação Synapse**

6. Para selecionar o utilizador, insira o seu **<service_principal_name>** e clique no seu principal serviço

7. Clique **em Aplicar** (aguarde 3 minutos para que a permissão entre em vigor.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Descarregue o Conector Azure Synheuse Prometheus

Utilize os comandos para instalar o Conector Azure Synapse Prometheus.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. Criar um ficheiro config para espaços de trabalho Azure Synapse

Crie um ficheiro config.yaml na pasta config e preencha os seguintes campos: workspace_name, tenant_id, service_principal_name e service_principal_password.
Pode adicionar vários espaços de trabalho no yaml config.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. Atualizar o Config Prometeu

Adicione a seguinte secção config no seu prometheus scrape_config e substitua a <your_workspace_name> no nome do seu espaço de trabalho e na <path_to_synapse_connector> à sua pasta clonada de conector de sinapse-prometheus

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Inicie o conector no VM do servidor Prometheus

Inicie um servidor de conector no VM do servidor Prometheus da seguinte forma.

```
python main.py
```

Aguarde alguns segundos e o conector deve começar a funcionar. E pode ver o "conector sinapse-prometheus" na página de descoberta do serviço Prometheus.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Utilize métricas Azure Synapse Prometheus ou REST para recolher dados métricos

### <a name="1-authentication"></a>1. Autenticação
Você pode usar o fluxo de credenciais do cliente para obter um token de acesso. Para aceder às métricas API, deverá obter um sinal de acesso AD AD Azure para o principal do serviço, que tem permissão adequada para aceder às APIs.

| Parâmetro     | Obrigatório | Descrição                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Verdadeiro     | O seu iD principal de serviço Azure (aplicação)                                                          |
| grant_type    | Verdadeiro     | Especifica o tipo de subvenção solicitado. Num fluxo de concessão de credenciais de cliente, o valor deve ser client_credentials. |
| client_id     | Verdadeiro     | O ID de aplicação (principal de serviço) da aplicação que registou no portal Azure ou no Azure CLI.        |
| client_secret | Verdadeiro     | O segredo gerado para a aplicação (diretor de serviço)                                                  |
| recurso      | Verdadeiro     | Synapse recursos uri, deve ser https://dev.azuresynapse.net '                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

A resposta parece:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. Lista de candidaturas em execução no espaço de trabalho Azure Synapse

Para obter uma lista de pedidos de faíscas para um espaço de trabalho da Sinaapse, pode seguir este documento [Monitoring - Get Spark Job List](/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. Recolher métricas de aplicação de faíscas com as APIs Prometeu ou REST


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Recolher métricas de aplicação de faíscas com a API Prometheus

Obtenha as métricas mais recentes da aplicação de faíscas especificada pela Prometheus API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parâmetro          | Obrigatório | Descrição                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Verdadeiro     | O ponto final de desenvolvimento do espaço de trabalho, por https://myworkspace.dev.azuresynapse.net exemplo. |
| livyApiVersion     | Verdadeiro     | Versão api válida para o pedido. Atualmente, é 2019-11-01-pré-visualização                    |
| sparkPoolName      | Verdadeiro     | O nome da piscina de faíscas.                                                                   |
| sessionId          | Verdadeiro     | Identificador para a sessão.                                                               |
| sparkApplicationId | Verdadeiro     | ID de aplicação de faísca                                                                      |

Pedido de amostra: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Resposta à amostra:

Código de estado: 200 Resposta parece:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Recolher métricas de aplicação de faíscas com a API REST

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parâmetro          | Obrigatório | Descrição                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Verdadeiro     | O ponto final de desenvolvimento do espaço de trabalho, por https://myworkspace.dev.azuresynapse.net exemplo. |
| livyApiVersion     | Verdadeiro     | Versão api válida para o pedido. Atualmente, é 2019-11-01-pré-visualização                    |
| sparkPoolName      | Verdadeiro     | O nome da piscina de faíscas.                                                                   |
| sessionId          | Verdadeiro     | Identificador para a sessão.                                                               |
| sparkApplicationId | Verdadeiro     | ID de aplicação de faísca                                                                      |

Pedido de Amostra

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Código de Estado da resposta da amostra: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. Construa as suas próprias ferramentas de diagnóstico e monitorização

A API Prometheus e as APIs REST fornecem dados de métricas ricos sobre a informação de execução da aplicação de faíscas. Pode recolher os dados de métricas relacionados com a aplicação através da API Prometheus e das APIs REST. E construa o seu próprio diagnóstico e ferramentas de monitorização que sejam mais adequadas às suas necessidades.
