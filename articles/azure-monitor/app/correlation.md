---
title: Azure Application Insights | de correlação de telemetria Microsoft Docs
description: Correlação de telemetria de insights de aplicação
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 50b858d0bf05aa46ea20a6cf9e088376be2996e3
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693431"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria em Insights de Aplicação

No mundo dos microserviços, cada operação lógica requer trabalho a ser feito em vários componentes do serviço. Pode monitorizar cada um destes componentes separadamente utilizando [o Application Insights](../../azure-monitor/app/app-insights-overview.md). A Application Insights suporta a correlação de telemetria distribuída, que utiliza para detetar qual o componente responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados utilizado pela Application Insights para correlacionar a telemetria enviada por vários componentes. Abrange técnicas e protocolos de propagação de contexto. Abrange também a implementação de táticas de correlação em diferentes línguas e plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de dados para a correlação de telemetria

O Application Insights define um [modelo de dados](../../azure-monitor/app/data-model.md) para a correlação de telemetria distribuída. Para associar a telemetria a uma operação lógica, cada item de telemetria tem um campo de contexto chamado `operation_Id` . Este identificador é partilhado por cada item de telemetria no vestígio distribuído. Assim, mesmo que perca a telemetria de uma única camada, ainda pode associar a telemetria relatada por outros componentes.

Uma operação lógica distribuída consiste tipicamente num conjunto de operações mais pequenas que são pedidos processados por um dos componentes. Estas operações são definidas por [telemetria de pedido.](../../azure-monitor/app/data-model-request-telemetry.md) Cada item de telemetria de pedido tem o seu próprio `id` que o identifica de forma única e global. E todos os itens de telemetria (tais como vestígios e exceções) que estejam associados ao pedido devem definir `operation_parentId` o valor do pedido `id` .

Todas as operações de saída, como uma chamada HTTP para outro componente, são representadas por [telemetria de dependência.](../../azure-monitor/app/data-model-dependency-telemetry.md) A telemetria de dependência também define a sua própria `id` que é globalmente única. Solicite telemetria, iniciada por esta chamada de dependência, usa isto `id` como `operation_parentId` seu .

Pode construir uma visão da operação lógica distribuída utilizando `operation_Id` `operation_parentId` , e com `request.id` `dependency.id` . Estes campos também definem a ordem de causalidade das chamadas de telemetria.

Num ambiente de microserviços, os vestígios de componentes podem ir para diferentes itens de armazenamento. Cada componente pode ter a sua própria chave de instrumentação em Application Insights. Para obter telemetria para a operação lógica, o Application Insights consulta dados de cada item de armazenamento. Quando o número de itens de armazenamento é grande, você precisará de uma pista sobre onde procurar a seguir. O modelo de dados Application Insights define dois campos para resolver este problema: `request.source` e `dependency.target` . O primeiro campo identifica o componente que iniciou o pedido de dependência. O segundo campo identifica qual componente devolveu a resposta da chamada de dependência.

## <a name="example"></a>Exemplo

Vejamos um exemplo. Uma aplicação chamada Stock Prices mostra o preço de mercado atual de uma ação através de uma API externa chamada Stock. A aplicação Preços de Stock tem uma página chamada Página de Stock que o navegador web do cliente abre usando `GET /Home/Stock` . A aplicação consulta a API de Stock utilizando a chamada HTTP `GET /api/stock/value` .

Pode analisar a telemetria resultante executando uma consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nos resultados, note que todos os itens de telemetria partilham a raiz `operation_Id` . Quando uma chamada do Ajax é feita a partir da página, um novo ID único `qJSXU` () é atribuído à telemetria de dependência, e o ID da páginaView é usado como `operation_ParentId` . O pedido do servidor utiliza então o ID do Ajax como `operation_ParentId` .

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| páginaVer   | Página de stock                |              | STYZ               | STYZ         |
| dependência | GET /Home/Stock           | qJSXU        | STYZ               | STYZ         |
| pedido    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYZ         |
| dependência | GET /api/stock/valor      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYZ         |

Quando a chamada `GET /api/stock/value` é feita para um serviço externo, precisa de saber a identidade desse servidor para que possa definir o campo `dependency.target` adequadamente. Quando o serviço externo não suporta a monitorização, `target` é definido para o nome de anfitrião do serviço (por exemplo, `stock-prices-api.com` ). Mas se o serviço se identificar devolvendo um cabeçalho HTTP predefinido, `target` contém a identidade de serviço que permite que a Application Insights construa um traço distribuído consultando telemetria a partir desse serviço.

## <a name="correlation-headers-using-w3c-tracecontext"></a>Cabeçalhos de correlação usando OContexto de Vestígios W3C

A Application Insights está em transição para [o Contexto de Rastreio W3C,](https://w3c.github.io/trace-context/)que define:

- `traceparent`: Transporta o ID de operação globalmente único e identificador único da chamada.
- `tracestate`: Transporta o contexto de rastreio específico do sistema.

A versão mais recente do Application Insights SDK suporta o protocolo Trace-Context, mas poderá ter de optar pelo mesmo. (A retrocompatibilidade com o protocolo de correlação anterior suportado pelo Application Insights SDK será mantida.)

O [protocolo HTTP de correlação, também chamado Request-Id,](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)está a ser depreciado. Este protocolo define dois cabeçalhos:

- `Request-Id`: Transporta a identificação globalmente única da chamada.
- `Correlation-Context`: Transporta a coleção de pares de valor-nome das propriedades de vestígios distribuídos.

Os Insights de Aplicação também definem a [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para o protocolo HTTP de correlação. Utiliza `Request-Context` pares de valor-nome para propagar a coleção de propriedades utilizadas pelo chamador ou callee imediato. O Application Insights SDK utiliza este cabeçalho para definir os `dependency.target` campos e `request.source` campos.

Os modelos de dados de rastreio e aplicação de rastreio [w3C](https://w3c.github.io/trace-context/) mapeiam da seguinte forma:

| Application Insights                   | Traçado W3C                                      |
|------------------------------------    |-------------------------------------------------|
| `Id` de `Request` e `Dependency`     | [id parental](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [trace-id](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | [parent-id](https://w3c.github.io/trace-context/#parent-id) do período de pais deste período. Se isto é uma extensão de raiz, então este campo deve estar vazio.     |

Para obter mais informações, consulte o [modelo de dados de telemetria Da Aplicação Insights](../../azure-monitor/app/data-model.md).

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>Ativar suporte de rastreio distribuído W3C para aplicações .NET

O rastreio distribuído baseado em W3C TraceContext é ativado por padrão em todos os SDKs de núcleo .NET/.NET, juntamente com compatibilidade retrógrada com o protocolo legado Request-Id.

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Ativar o suporte de rastreio distribuído pela W3C para aplicações Java

#### <a name="java-30-agent"></a>Java 3.0 agente

  O agente Java 3.0 suporta o W3C fora da caixa e não é necessária nenhuma configuração adicional. 

#### <a name="java-sdk"></a>SDK Java
- **Configuração de entrada**

  - Para aplicações Java EE, adicione o seguinte à `<TelemetryModules>` etiqueta em ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```

  - Para aplicações Boot de mola, adicione estas propriedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuração de saída**

  Adicione o seguinte a AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > O modo de compatibilidade retrógrada é ativado por predefinição e o `enableW3CBackCompat` parâmetro é opcional. Use-o apenas quando quiser desligar a compatibilidade para trás.
  >
  > Idealmente, você iria desativar isto quando todos os seus serviços foram atualizados para versões mais recentes de SDKs que suportam o protocolo W3C. Recomendamos vivamente que se mude para estes Novos SDKs o mais rápido possível.

> [!IMPORTANT]
> Certifique-se de que as configurações de entrada e saída são exatamente as mesmas.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Ativar suporte de rastreio distribuído W3C para aplicações Web

Esta funcionalidade está em `Microsoft.ApplicationInsights.JavaScript` . É desativado por defeito. Para o ativar, utilize `distributedTracingMode` config. AI_AND_W3C é fornecida para retrocompatibilidade com quaisquer serviços legados instrumentados pela Application Insights.

- **[configuração baseada em npm](./javascript.md#npm-based-setup)**

Adicione a seguinte configuração:
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```

- **[Configuração baseada em snippet](./javascript.md#snippet-based-setup)**

Adicione a seguinte configuração:
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> Para ver todas as configurações necessárias para permitir a correlação, consulte a documentação de [correlação JavaScript](./javascript.md#enable-correlation).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlação de telemetria em OpenCensus Python

O OpenCensus Python suporta [o contexto de rastreio W3C](https://w3c.github.io/trace-context/) sem necessitar de configuração adicional.

Como referência, o modelo de dados OpenCensus pode ser consultado [aqui.](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace)

### <a name="incoming-request-correlation"></a>Correlação de pedido de entrada

O OpenCensus Python correlaciona os cabeçalhos de Trace-Context W3C de pedidos de entrada para os vãos que são gerados a partir dos próprios pedidos. O OpenCensus fá-lo-á automaticamente com integrações para estes quadros populares de aplicações web: Flask, Django e Pirâmide. Basta preencher os cabeçalhos de Trace-Context W3C com o [formato correto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e enviá-los com o pedido. Aqui está uma aplicação de amostra flask que demonstra isto:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Este código executa uma aplicação de amostras de frasco na sua máquina local, ouvindo a porta `8080` . Para correlacionar o contexto de rastreio, envia um pedido para o ponto final. Neste exemplo, pode utilizar um `curl` comando:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Ao olhar para o [formato do cabeçalho Trace-Context,](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)pode obter as seguintes informações:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se olhar para a entrada de pedido que foi enviada para o Azure Monitor, pode ver campos povoados com a informação do cabeçalho de traços. Pode encontrar estes dados em Registos (Analytics) no recurso Azure Monitor Application Insights.

![Solicitar telemetria em Logs (Analytics)](./media/opencensus-python/0011-correlation.png)

O `id` campo está no formato , onde o é retirado do `<trace-id>.<span-id>` `trace-id` cabeçalho de traço que foi passado no pedido e `span-id` o é um conjunto gerado de 8 bytes para este período.

O `operation_ParentId` campo está no formato , onde tanto o e o são `<trace-id>.<parent-id>` `trace-id` `parent-id` retirados do cabeçalho de traço que foi passado no pedido.

### <a name="log-correlation"></a>Correlação de registos

O OpenCensus Python permite-lhe correlacionar registos adicionando um ID de vestígios, uma identificação de envergadura e uma bandeira de amostragem para registar registos. Adiciona estes atributos instalando a [integração de registos](https://pypi.org/project/opencensus-ext-logging/)OpenCensus . Os seguintes atributos serão adicionados aos `LogRecord` objetos Python: `traceId` , e `spanId` `traceSampled` . Note que isto só produz efeitos para os madeireiros que são criados após a integração.

Aqui está uma aplicação de amostra que demonstra isto:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quando este código é executado, as seguintes impressões na consola:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Note que há um `spanId` presente para a mensagem de registo que está dentro do intervalo. Isto é o mesmo `spanId` que pertence ao período chamado `hello` .

Pode exportar os dados de registo utilizando `AzureLogHandler` . Para obter mais informações, consulte [este artigo](./opencensus-python.md#logs).

Também podemos passar informações de um componente para outro para uma correlação adequada. Por exemplo, considere um cenário em que existam dois componentes `module1` e `module2` . O Módulo1 chama as funções no Módulo2 e para obter registos de ambos `module1` e num único vestígio `module2` podemos usar a seguinte abordagem:

```python
# module1.py
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer
from module2 import function_1

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
   logger.warning('In the span')
   function_1(tracer)
logger.warning('After the span')


# module2.py

import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

def function_1(parent_tracer=None):
    if parent_tracer is not None:
        tracer = Tracer(
                    span_context=parent_tracer.span_context,
                    sampler=AlwaysOnSampler(),
                )
    else:
        tracer = Tracer(sampler=AlwaysOnSampler())

    with tracer.span("function_1"):
        logger.info("In function_1")
```

## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria em .NET

.NET suportes de tempo de execução distribuídos com a ajuda de [Atividade](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) e [DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)

O Application Insights .NET SDK utiliza `DiagnosticSource` e para recolher e `Activity` correlacionar a telemetria.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Correlação de telemetria em Java

[O agente Java,](./java-in-process-agent.md) bem como a versão [Java SDK](../../azure-monitor/app/java-get-started.md) 2.0.0 ou posteriormente suportam a correlação automática de telemetria. Ele povoa automaticamente `operation_id` para toda a telemetria (como vestígios, exceções e eventos personalizados) emitidos no âmbito de um pedido. Também propaga os cabeçalhos de correlação (descritos anteriormente) para chamadas de serviço-a-serviço via HTTP, se o [agente Java SDK](../../azure-monitor/app/java-agent.md) estiver configurado.

> [!NOTE]
> Application Insights Java agente coleta automaticamente pedidos e dependências para JMS, Kafka, Netty/Webflux, e muito mais. Para Java SDK apenas as chamadas feitas via Apache HttpClient são suportadas para a função de correlação. A propagação automática do contexto através de tecnologias de mensagens (como Kafka, RabbitMQ e Azure Service Bus) não é suportada no SDK. 

> [!NOTE]
> Para recolher telemetria personalizada, é necessário instrumentar a aplicação com Java 2.6 SDK. 

### <a name="role-names"></a>Nomes de papéis

É melhor personalizar a forma como os nomes dos componentes são apresentados no Mapa de [Aplicações.](../../azure-monitor/app/app-map.md) Para tal, pode definir manualmente o `cloud_RoleName` através de uma das seguintes ações:

- Para o agente Java 3.0, desaprove o nome da função de nuvem da seguinte forma:

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    Também pode definir o nome da função de nuvem usando a variável `APPLICATIONINSIGHTS_ROLE_NAME` ambiente.

- Com o Application Insights Java SDK 2.5.0 e mais tarde, pode especificar o `cloud_RoleName` através da adição `<RoleName>` ao seu ficheiro ApplicationInsights.xml:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se utilizar o Boot De primavera com o Arranque de Arranque de Mola Do Application Insights, basta definir o seu nome personalizado para a aplicação no ficheiro aplicações.properties:

  `spring.application.name=<name-of-app>`

  O Arranque de Arranque de Mola atribui automaticamente `cloudRoleName` ao valor que introduz para a `spring.application.name` propriedade.

## <a name="next-steps"></a>Passos seguintes

- Escreva [telemetria personalizada.](../../azure-monitor/app/api-custom-events-metrics.md)
- Para cenários de correlação avançados em ASP.NET Core e ASP.NET, consulte [as operações personalizadas track](custom-operations-tracking.md).
- Saiba mais sobre [a definição cloud_RoleName](./app-map.md#set-or-override-cloud-role-name) para outros SDKs.
- A bordo de todos os componentes do seu microserviço no Application Insights. Confira as [plataformas suportadas.](./platforms.md)
- Consulte o [modelo de dados](./data-model.md) para os tipos de Insights de Aplicação.
- Saiba como [estender e filtrar a telemetria.](./api-filtering-sampling.md)
- Reveja a [referência config do Application Insights config](configuration-with-applicationinsights-config.md).