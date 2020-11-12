---
title: Métricas Advisor Java quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 7c2c6460207538371af2a1deada524313816793b
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523805"
---
[Documentação de referência](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src)  |  [Artefacto (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.
* Assim que tiver a sua subscrição Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Crie um recurso Metrics Advisor crie um "  target="_blank"> recurso Metrics Advisor no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para implementar a sua instância Metrics Advisor.  
* A sua própria base de dados SQL com dados de séries de tempo.
  
  
> [!TIP]
> * Pode encontrar amostras do Java Metrics Advisor no [GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)
> * Pode levar 10 a 30 minutos para o seu recurso Metrics Advisor implementar uma instância de serviço para que possa utilizar. Clique **em Ir para o recurso** assim que implementar com sucesso. Após a implementação, pode começar a utilizar a sua instância Metrics Advisor com o portal web e a API REST. 
> * Pode encontrar o URL para a API REST no portal Azure, na secção **Visão Geral** do seu recurso. Terá o seguinte aspeto:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Configuração

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Este quickstart usa o gestor de dependência gradle. Pode encontrar mais informações sobre a biblioteca de clientes no [Repositório Central de Maven.](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o comando do seu diretório de `gradle init` trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts* que é usado em tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando solicitado para escolher um **DSL,** selecione **Kotlin**.

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Localize *build.gradle.kts* e abra-o com o seu IDE ou editor de texto preferido. Em seguida, copie nesta configuração de construção. Certifique-se de incluir as dependências do projeto.

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.2")
}
```

### <a name="create-a-java-file"></a>Criar um ficheiro Java

Crie uma pasta para a sua aplicação de amostra. Do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir -p src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *MetricsAdvisorQuickstarts.java*. Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)que contém os exemplos de código neste arranque rápido.

Na classe da `MetricsAdvisorQuickstarts` aplicação, crie variáveis para a chave e ponto final do seu recurso.


> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso Metrics Advisor que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar as chaves de subscrição e o ponto final na página **Chave e Ponta final** do recurso, na **Gestão de Recursos.** <br><br>Para recuperar a sua chave API tem de ir a [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Selecione o apropriado: **Diretório,** **Subscrições** e **Espaço de Trabalho** para o seu recurso e escolha Começar a **trabalhar**. Poderá então recuperar as suas chaves API de [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) dos Serviços Cognitivos para obter mais informações.

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

No método da `Main()` aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Vais criar isto mais tarde.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes aulas lidam com algumas das principais características do Conselheiro de Métricas Java SDK.

|Nome|Descrição|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **Usado para:** <br> - Enumeração de incidentes de anomalia <br> - Listagem da causa principal dos incidentes <br> - Recuperação dos dados originais das séries de tempo e dos dados das séries de tempo enriquecidos pelo serviço. <br> - Alertas de listagem <br> - Adicionar feedback para sintonizar o seu modelo |
| [MétricasAdvisorDissinsecimento](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **Permite-lhe:** <br> - Gerir feeds de dados <br> - Configurações de deteção de anomalias configurantes configurações de configuração de anomalias <br> - Configurações de alerta de anomalias configurações de alerta de anomalias configurações <br> - Gerir ganchos  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeed.html) | **O que o Metrics Advisor ingere a partir da sua fonte de dados. A `DataFeed` contém linhas de:** <br> - Prazos <br> - Dimensões zero ou mais <br> - Uma ou mais medidas  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeedMetric.html) | A `DataFeedMetric` é uma medida quantificável que é usada para monitorizar e avaliar o estado de um processo de negócio específico. Pode ser uma combinação de múltiplos valores de séries de tempo divididos em dimensões. Por exemplo, uma métrica de saúde web pode conter dimensões para a contagem de utilizadores e para o mercado en-us. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Metrics Advisor para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Adicionar um feed de dados](#add-a-data-feed)
* [Verifique o estado da ingestão](#check-the-ingestion-status)
* [Deteção de anomalias de configuração](#configure-anomaly-detection)
* [Criar um gancho](#create-a-hook)
* [Criar uma configuração de alerta](#create-an-alert-configuration)
* [Consultar o alerta](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticar o cliente

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>Criar um cliente métrica advisor usando MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>Criar um cliente da Administração de Métricas utilizando o MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>Adicionar um feed de dados

`sql_server_connection_string`Substitua-o pela sua própria cadeia de ligação ao servidor SQL e substitua-os `query` por uma consulta que retorne os seus dados a um único tempo. Também terá de ajustar os `DataFeedMetric` valores e `DataFeedDimension` valores com base nos seus dados personalizados.

> [!IMPORTANT]
> A consulta deve voltar no máximo um registo para cada combinação de dimensão, em cada hora de temperatura. E todos os registos devolvidos pela consulta devem ter os mesmos tempos. O Metrics Advisor executará esta consulta para cada timetamp para ingerir os seus dados. Consulte a [secção faq em consultas](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) para obter mais informações e exemplos. 

```java
DataFeed dataFeed = new DataFeed()
    .setName("dataFeedName")
    .setSource(new MySqlDataFeedSource("sql_server_connection_string", "query"))
    .setGranularity(new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY))
    .setSchema(new DataFeedSchema(
        Arrays.asList(
            new DataFeedMetric().setName("cost"),
            new DataFeedMetric().setName("revenue")
        )).setDimensions(
        Arrays.asList(
            new DataFeedDimension().setName("city"),
            new DataFeedDimension().setName("category")
        ))
    )
    .setIngestionSettings(new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")))
    .setOptions(new DataFeedOptions()
        .setDescription("data feed description")
        .setRollupSettings(new DataFeedRollupSettings()
            .setRollupType(DataFeedRollupType.AUTO_ROLLUP)));
final DataFeed createdSqlDataFeed = metricsAdvisorAdminClient.createDataFeed(dataFeed);

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(System.out::println);
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());

if (SQL_SERVER_DB == createdSqlDataFeed.getSourceType()) {
    System.out.printf("Data feed sql server query: %s%n",
        ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
}
```

## <a name="check-the-ingestion-status"></a>Verifique o estado da ingestão

Este exemplo verifica o estado de ingestão de uma fonte de alimentação de dados previamente fornecida.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdminClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>Deteção de anomalias de configuração 

Este exemplo demonstra como um utilizador pode configurar uma configuração de deteção de anomalias para os seus dados.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdminClient.createMetricAnomalyDetectionConfig(
        metricId,
        new AnomalyDetectionConfiguration("My dataPoint anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>Criar um gancho

Este exemplo cria um gancho de e-mail que recebe alertas de incidentes de anomalia.

```java
NotificationHook emailNotificationHook = new EmailNotificationHook("email Hook")
    .setDescription("my email Hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://example.com/handleAlerts"); // you must enter a valid webhook url to post the alert payload

final NotificationHook notificationHook = metricsAdvisorAdminClient.createHook(emailNotificationHook);
EmailNotificationHook createdEmailHook = (EmailNotificationHook) notificationHook;
System.out.printf("Email Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Email Hook name: %s%n", createdEmailHook.getName());
System.out.printf("Email Hook description: %s%n", createdEmailHook.getDescription());
System.out.printf("Email Hook external Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Email Hook emails to alert: %s%n",
    String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>Criar uma configuração de alerta

Este exemplo demonstra como um utilizador pode configurar uma configuração de alerta para anomalias detetadas nos seus dados.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdminClient.createAnomalyAlertConfig(
        new AnomalyAlertConfiguration("My anomaly alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityRangeCondition(new SeverityCondition()
                                .setMaxAlertSeverity(AnomalySeverity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>Consultar o alerta

Este exemplo demonstra como um utilizador pode consultar alertas desencadeados para uma configuração de deteção de alerta e obter anomalias para esse alerta.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
final OffsetDateTime startTime = OffsetDateTime.parse("2020-01-01T00:00:00Z");
final OffsetDateTime endTime = OffsetDateTime.parse("2020-09-09T00:00:00Z");
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
        startTime, endTime)
    .forEach(alert -> {
        System.out.printf("AnomalyAlert Id: %s%n", alert.getId());
        System.out.printf("AnomalyAlert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("DataPoint Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("DataPoint Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("DataPoint Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("DataPoint Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

Pode construir a aplicação com:

```console
gradle build
```
### <a name="run-the-application"></a>Executar a aplicação

Executar a aplicação com o `run` objetivo:

```console
gradle run
```