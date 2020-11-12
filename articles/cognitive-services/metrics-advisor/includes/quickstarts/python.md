---
title: Métricas Advisor Python quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2c79773d6697ae9fb62e2b7515da60178243fe40
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523843"
---
[Documentação de referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md)  |  [Pacote (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Assim que tiver a sua subscrição Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Crie um recurso Metrics Advisor crie um "  target="_blank"> recurso Metrics Advisor no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para implementar a sua instância Metrics Advisor.  
* A sua própria base de dados SQL com dados de séries de tempo.
  
> [!TIP]
> * Você pode encontrar amostras do Python Metrics Advisor no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * Pode levar 10 a 30 minutos para o seu recurso Metrics Advisor implementar uma instância de serviço para que possa utilizar. Clique **em Ir para o recurso** assim que implementar com sucesso. Após a implementação, pode começar a utilizar a sua instância Metrics Advisor com o portal web e a API REST.
> * Pode encontrar o URL para a API REST no portal Azure, na secção **Visão Geral** do seu recurso. Terá o seguinte aspeto:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a biblioteca do cliente com:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie um novo ficheiro Python e importe as seguintes bibliotecas.

```python
import os
import datetime
```

Crie variáveis para o ponto final e chave Azure do seu recurso.

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso Metrics Advisor que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar as chaves de subscrição e o ponto final na página **Chave e Ponta final** do recurso, na **Gestão de Recursos.** <br><br>Para recuperar a sua chave API tem de ir a [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Selecione o apropriado: **Diretório,** **Subscrições** e **Espaço de Trabalho** para o seu recurso e escolha Começar a **trabalhar**. Poderá então recuperar as suas chaves API de [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) dos Serviços Cognitivos para obter mais informações.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes lidam com algumas das principais características do Metrics Advisor Python SDK.

|Nome|Descrição|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Usado para:** <br> - Listagem de incidentes <br> - Listagem da causa principal dos incidentes <br> - Recuperação dos dados originais das séries de tempo e dos dados das séries de tempo enriquecidos pelo serviço. <br> - Alertas de listagem <br> - Adicionar feedback para sintonizar o seu modelo |
| [MétricasAdvisorDissinsecimento](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **Permite-lhe:** <br> - Gerir feeds de dados <br> - Criar, configurar, recuperar, listar e eliminar configurações de deteção de anomalias <br> - Criar, configurar, recuperar, listar e eliminar configurações de alerta de anomalias <br> - Gerir ganchos  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **O que o Metrics Advisor ingere a partir da sua fonte de dados. A `DataFeed` contém linhas de:** <br> - Prazos <br> - Dimensões zero ou mais <br> - Uma ou mais medidas  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | A `DataFeedMetric` é uma medida quantificável que é usada para monitorizar e avaliar o estado de um processo de negócio específico. Pode ser uma combinação de múltiplos valores de séries de tempo divididos em dimensões. Por exemplo, uma métrica de saúde web pode conter dimensões para a contagem de utilizadores e para o mercado en-us. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Metrics Advisor para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Adicionar um feed de dados](#add-a-data-feed)
* [Verifique o estado da ingestão](#check-the-ingestion-status)
* [Configuração de deteção de configuração e configuração de alerta](#configure-anomaly-detection)
* [Criar uma configuração de alerta](#create-an-alert-configuration)
* [Resultados de deteção de anomalias de consulta](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticar o cliente

O cliente neste exemplo é um `MetricsAdvisorAdministrationClient` objeto que usa o seu ponto final e um objeto que contém as suas `MetricsAdvisorKeyCredential` chaves. Não precisas de copiar esta amostra de código. Os métodos que criar mais tarde irão instantaneamente o cliente. O cliente alternativo é chamado `MetricsAdvisorClient` mais informações sobre este cliente pode ser encontrado na [documentação de referência.](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient)

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Adicionar um feed de dados

Num novo método, crie declarações de importação como o exemplo abaixo. `sql_server_connection_string`Substitua-o pela sua própria cadeia de ligação ao servidor SQL e substitua-os `query` por uma consulta que retorne os seus dados a um único tempo. Também terá de ajustar os `DataFeedmetric` valores e `DataFeedDimension` valores com base nos seus dados personalizados.

> [!IMPORTANT]
> A consulta deve voltar no máximo um registo para cada combinação de dimensão, em cada hora de temperatura. E todos os registos devolvidos pela consulta devem ter os mesmos tempos. O Metrics Advisor executará esta consulta para cada timetamp para ingerir os seus dados. Consulte a [secção faq em consultas](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) para obter mais informações e exemplos. 

Crie um cliente com as suas chaves e ponto final, e use `client.create_data_feed()` para configurar o nome, fonte, granularidade e esquema. Também pode definir o tempo de ingestão, as definições de rollup e muito mais.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Verifique o estado da ingestão

Num novo método, crie uma declaração de importação como o exemplo abaixo. `data_feed_id`Substitua-o pelo ID pelo feed de dados que criou. Crie um cliente com as suas chaves e ponto final, e use `client.list_data_feed_ingestion_status()` para obter o progresso da ingestão. Imprima os detalhes, como os últimos cartões de tempo ativos e bem-sucedidos.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Deteção de anomalias de configuração

Num novo método, crie declarações de importação como o exemplo abaixo. `metric_id`Substitua-o pela métrica que pretende configurar. Crie um cliente com as suas chaves e ponto final e use `client.create_detection_configuration` para criar uma nova configuração de deteção. As condições de limiar especificam os parâmetros para a deteção de anomalias.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Criar um gancho

Num novo método, crie declarações de importação como o exemplo abaixo. Crie um cliente com as suas chaves e ponto final, e use `client.create_hook()` para criar um gancho. Introduza uma descrição, uma lista de e-mails para enviar o alerta e um link externo para receber o alerta.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Criar uma configuração de alerta

Num novo método, crie declarações de importação como o exemplo abaixo. `detection_configuration_id`Substitua-o pela configuração de deteção de anomalias e `hook_id` substitua-o pelo gancho que criou anteriormente. Crie um cliente com as suas chaves e ponto final e use `client.create_alert_configuration()` para criar uma configuração de alerta. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Consultar o alerta

Num novo método, crie uma declaração de importação como o exemplo abaixo. `alert_id`Substitua-o pelo ID para o seu alerta e `alert_config_id` substitua-o pelo ID de configuração de alerta. Crie um cliente com as suas chaves e ponto final, e use `client.list_anomalies` para listar as anomalias para um alerta. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `python` comando no seu ficheiro quickstart.

```console
python quickstart-file.py
```