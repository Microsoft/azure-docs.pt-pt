---
title: Monitorize as aplicações java em qualquer ambiente - Azure Monitor Application Insights
description: Monitorização do desempenho da aplicação para aplicações Java em qualquer ambiente sem instrumentar a aplicação. Mapa de rastreio e aplicação distribuídos.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 1182813c0b79d43c2c264482629ad97f23683a49
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215285"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java aplicação codificada monitorizando Azure Monitor Application Insights - visualização pública

A monitorização da aplicação sem código java tem tudo a ver com simplicidade - não há alterações de código, o agente Java pode ser ativado através de apenas algumas alterações de configuração.

 O agente Java trabalha em qualquer ambiente, e permite-lhe monitorizar todas as suas aplicações Java. Por outras palavras, quer esteja a executar as suas aplicações Java em VMs, no local, em AKS, no Windows, Linux - diga o que quiser, o agente Java 3.0 irá monitorizar a sua aplicação.

A adição da Aplicação Insights Java SDK à sua aplicação já não é necessária, uma vez que o agente 3.0 recolhe automaticamente pedidos, dependências e registos por si só.

Ainda pode enviar telemetria personalizada a partir da sua aplicação. O agente 3.0 irá rastreá-lo e correlacioná-lo juntamente com toda a telemetria automática.

O agente 3.0 suporta Java 8 ou superior.

## <a name="quickstart"></a>Início Rápido

**1. Descarregue o agente**

Baixar [aplicaçõesinsights-agent-3.0.0-PREVIEW.7.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.7/applicationinsights-agent-3.0.0-PREVIEW.7.jar)

**2. Aponte o JVM ao agente**

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.7.jar` aos args JVM da sua aplicação

Os arg típicos jvm incluem `-Xmx512m` e `-XX:+UseG1GC` . Então, se sabe onde adicionar isto, então já sabe onde adicionar isto.

Para obter ajuda adicional para configurar os args JVM da sua aplicação, consulte [3.0 Preview: Dicas para atualizar os seus args JVM](./java-standalone-arguments.md).

**3. Aponte o agente para o seu recurso Application Insights**

Se ainda não tiver um recurso Application Insights, pode criar um novo seguindo os passos no guia de [criação de recursos.](./create-new-resource.md)

Aponte o agente para o seu recurso Application Insights, quer definindo uma variável ambiental:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Ou criando um ficheiro de configuração denominado `ApplicationInsights.json` , e colocando-o no mesmo diretório `applicationinsights-agent-3.0.0-PREVIEW.7.jar` que, com o seguinte conteúdo:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Pode encontrar a sua cadeia de ligação no seu recurso Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Cadeia de conexão de insights de aplicação":::

**4. É isto!**

Agora inicie a sua aplicação e vá ao seu recurso Application Insights no portal Azure para ver os seus dados de monitorização.

> [!NOTE]
> Pode levar alguns minutos para que os seus dados de monitorização apareçam no portal.


## <a name="configuration-options"></a>Opções de configuração

No `ApplicationInsights.json` ficheiro, pode ainda configurar:

* Nome do papel da nuvem
* Instância de papel em nuvem
* Captura de registo de aplicações
* Métricas JMX
* Micrometer
* Heartbeat
* Amostragem
* HTTP Proxy
* Autodiagnóssis

Consulte os detalhes em [3.0 Visualização pública: Opções de configuração](./java-standalone-config.md).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Pedidos, dependências, registos e métricas autocolecedes

### <a name="requests"></a>Pedidos

* Consumidores JMS
* Consumidores kafka
* Netty/WebFlux
* Servlets
* Agendamento da primavera

### <a name="dependencies-with-distributed-trace-propagation"></a>Dependências com propagação de vestígios distribuídos

* Apache HttpClient e HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Cliente Netty
* OkHttp

### <a name="other-dependencies"></a>Outras dependências

* Cassandra
* JDBC
* MongoDB (assíco e sincronização)
* Redis (Alface e Jedis)

### <a name="logs"></a>Registos

* java.util.logging
* Log4j (incluindo propriedades MDC)
* SLF4J/Logback (incluindo propriedades MDC)

### <a name="metrics"></a>Métricas

* Micrometro (incluindo métricas do actuador de bota de mola)
* Métricas JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Envio de telemetria personalizada a partir da sua aplicação

O nosso objetivo em 3.0+ é permitir-lhe enviar a sua telemetria personalizada usando APIs padrão.

Apoiamos o Micrometro, a API openTelemetry e os quadros populares de registo. O Application Insights Java 3.0 irá capturar automaticamente a telemetria e correlacioná-la juntamente com toda a telemetria automática.

### <a name="supported-custom-telemetry"></a>Telemetria personalizada suportada

O quadro abaixo representa os tipos de telemetria personalizados atualmente suportados que pode permitir complementar o agente Java 3.0. Resumindo, as métricas personalizadas são suportadas através de micrometros, as exceções personalizadas e os vestígios podem ser ativados através de quadros de registo, e qualquer tipo de telemetria personalizada é suportada através do [Application Insights Java 2.x SDK](#sending-custom-telemetry-using-application-insights-java-sdk-2x). 

|                     | Micrometer | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Eventos Personalizados**   |            |                     |  Yes    |
| **Métricas Personalizadas**  |  Yes       |                     |  Yes    |
| **Dependências**    |            |                     |  Yes    |
| **Exceções**      |            |  Yes                |  Yes    |
| **Vistas de página**      |            |                     |  Yes    |
| **Pedidos**        |            |                     |  Yes    |
| **Rastreios**          |            |  Yes                |  Yes    |

Não estamos a planear lançar um SDK com o Application Insights 3.0 neste momento.

Application Insights Java 3.0 já está a ouvir a telemetria que é enviada para a Aplicação Insights Java SDK 2.x. Esta funcionalidade é uma parte importante da história de upgrade para os utilizadores existentes em 2.x, e preenche uma lacuna importante no nosso suporte de telemetria personalizado até que a API OpenTelemetry seja GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Envio de telemetria personalizada usando Application Insights Java SDK 2.x

Adicione `applicationinsights-core-2.6.0.jar` à sua aplicação (todas as versões 2.x são suportadas pela Application Insights Java 3.0, mas vale a pena usar as últimas se tiver escolha):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Criar um TelemetriaClient:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usá-lo para enviar telemetria personalizada.

### <a name="events"></a>Eventos

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Métricas

Pode enviar telemetria métrica através [do Micrometro:](https://micrometer.io)

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Ou também pode utilizar a Aplicação Insights Java SDK 2.x:

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
Pode enviar telemetria de registo personalizado através da sua estrutura de registo favorito.

Ou também pode utilizar a Aplicação Insights Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Exceções
Pode enviar telemetria de exceção personalizada através da sua estrutura de registo favorito.

Ou também pode utilizar a Aplicação Insights Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgrade a partir de Application Insights Java SDK 2.x

Se já estiver a utilizar o Application Insights Java SDK 2.x na sua aplicação, não há necessidade de a remover. O agente Java 3.0 irá detetá-lo e capturar e correlacionar qualquer telemetria personalizada que está a enviar através do Java SDK 2.x, enquanto suprimiu qualquer autocolecção realizada pelo Java SDK 2.x para evitar a captura duplicada.

Se estava a usar o agente Application Insights 2.x, tem de remover o `-javaagent:` arg JVM que estava a apontar para o agente 2.x.

> [!NOTE]
> Nota: Java SDK 2.x TelemetriaInitializadores e TelemetriaProcessadores não serão executados quando utilizar o agente 3.0.
