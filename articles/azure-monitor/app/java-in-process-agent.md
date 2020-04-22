---
title: Monitorize aplicações java em qualquer ambiente - Insights de aplicação do Monitor Azure
description: Monitorização do desempenho da aplicação para aplicações Java em qualquer ambiente sem instrumentar a aplicação. Mapa de rastreio e aplicação distribuído.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: b9c1a52051e63beee9a784714a7bb1a6a79e8759
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687721"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java codeless application monitoring Azure Monitor Application Insights - pré-visualização pública

A monitorização da aplicação sem código Java tem tudo a ver com simplicidade - não existem alterações de código, o agente Java pode ser ativado através de apenas algumas alterações de configuração.

 O agente Java trabalha em qualquer ambiente, e permite-lhe monitorizar todas as suas aplicações Java. Por outras palavras, quer esteja a executar as suas aplicações Java em VMs, no local, no AKS, no Windows, Linux - o que quiser, o agente Java 3.0 irá monitorizar a sua aplicação.

A adição dos Insights de Aplicação Java SDK à sua aplicação já não é necessária, uma vez que o agente 3.0 recolhe pedidos, dependências e registos por si só.

Ainda pode enviar telemetria personalizada da sua aplicação. O agente 3.0 irá rastreá-lo e correlacionar-lo juntamente com toda a telemetria auto-recolhida.

## <a name="quickstart"></a>Início Rápido

**1. Descarregue o agente**

Baixar [applicationinsights-agent-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. Indicar o JVM ao agente**

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar` ao JVM args da sua aplicação

Os típicos args `-Xmx512m` `-XX:+UseG1GC`JVM incluem e . Então, se sabe onde adicionar isto, então já sabe onde adicionar isto.

Para obter ajuda adicional para configurar os args JVM da sua aplicação, consulte [o 3.0 Preview: Dicas para atualizar o seu Args JVM](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments).

**3. Aponte o agente para o seu recurso Insights de Aplicação**

Se ainda não tiver um recurso Application Insights, pode criar um novo seguindo os passos no guia de criação de [recursos.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

Aponte o agente para o seu recurso Application Insights, quer através da definição de uma variável ambiental:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Ou criando um ficheiro `ApplicationInsights.json`de configuração chamado , `applicationinsights-agent-3.0.0-PREVIEW.3.jar`e colocando-o no mesmo diretório que, com o seguinte conteúdo:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Pode encontrar a sua cadeia de ligação no seu recurso Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Linha de ligação insights de aplicação":::

**4. É isto!**

Agora inicie a sua aplicação e vá ao seu recurso Application Insights no portal Azure para ver os seus dados de monitorização.

> [!NOTE]
> Pode levar alguns minutos para os seus dados de monitorização aparecerem no portal.


## <a name="configuration-options"></a>Opções de configuração

No `ApplicationInsights.json` ficheiro, pode configurar adicionalmente:

* Nome de papel de nuvem
* Instância de papel de nuvem
* Captura de registo de aplicações
* Métricas JMX
* Micrometer
* Heartbeat
* Amostragem
* HTTP Proxy
* Autodiagnóstico

Consulte os detalhes em [3.0 Visualização Pública: Opções](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config)de Configuração .

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Pedidos, dependências, registos e métricas recolhidos automaticamente

### <a name="requests"></a>Pedidos

* Consumidores jMS
* Consumidores de Kafka
* Rede/WebFlux
* Servlets
* Agendamento da primavera

### <a name="dependencies-with-distributed-trace-propagation"></a>Dependências com propagação de vestígios distribuídos

* Apache HttpClient e HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Cliente netty
* OkHttp

### <a name="other-dependencies"></a>Outras dependências

* Cassandra
* JDBC
* MongoDB (asincronização e sincronização)
* Redis (Alface e Jedis)

### <a name="logs"></a>Registos

* java.util.logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Métricas

* Micrometer
* Métricas JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Envio de telemetria personalizada a partir da sua aplicação

O nosso objetivo em 3.0+ é permitir-lhe enviar a sua telemetria personalizada usando APIs padrão.

Apoiamos o Micrometro, a OpenTelemettry API e as populares estruturas de exploração madeireira. Aplicação Insights Java 3.0 capturará automaticamente a telemetria e correlacionará-a juntamente com toda a telemetria auto-recolhida.

Por esta razão, não estamos a planear lançar um SDK com Application Insights 3.0 neste momento.

Application Insights Java 3.0 já está a ouvir a telemetria que é enviada para a Aplicação Insights Java SDK 2.x. Esta funcionalidade é uma parte importante da história de upgrade para os utilizadores 2.x existentes, e preenche uma lacuna importante no nosso suporte de telemetria personalizado até que a API OpenTelemettry seja GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Envio de telemetria personalizada usando Insights de Aplicação Java SDK 2.x

Adicione `applicationinsights-core-2.6.0.jar` à sua aplicação (todas as versões 2.x são suportadas pela Application Insights Java 3.0, mas vale a pena usar as mais recentes se tiver escolha):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Criar um TelemettryClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usá-lo para enviar telemetria personalizada.

### <a name="events"></a>Eventos

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Métricas

Pode enviar telemetria métrica via [Micrometro:](https://micrometer.io)

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Ou também pode utilizar Insights de Aplicação Java SDK 2.x:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Dependências

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Registos
Pode enviar uma telemetria de log personalizada através da sua estrutura de registo favorita.

Ou também pode utilizar Insights de Aplicação Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Exceções
Pode enviar telemetria de exceção personalizada através da sua estrutura de registo favorita.

Ou também pode utilizar Insights de Aplicação Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgrade de Application Insights Java SDK 2.x

Se já está a utilizar insights de aplicação Java SDK 2.x na sua aplicação, não há necessidade de removê-lo. O agente Java 3.0 irá detetá-lo e capturá-lo e correlacionar qualquer telemetria personalizada que envie através do Java SDK 2.x, enquanto suprimia qualquer auto-recordação realizada pelo Java SDK 2.x para evitar a captura de duplicados.

> [!NOTE]
> Nota: Java SDK 2.x TelemettryInitializers e TelemettryProcessors não serão executados quando utilizaro agente 3.0.
