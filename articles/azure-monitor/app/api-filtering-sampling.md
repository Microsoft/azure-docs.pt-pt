---
title: Filtragem e processamento prévio no que o Azure Application Insights SDK | Documentos da Microsoft
description: Escreva telemetria processadores e inicializadores de telemetria para o SDK para filtrar ou adicionar propriedades para os dados antes da telemetria é enviada para o portal do Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 1b55a2b053b86d3260fdca201357445d2556c444
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082471"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtragem e processamento prévio de telemetria no Application Insights SDK


Pode escrever e configurar o plug-ins para o Application Insights SDK personalizar como a telemetria é capturada e processada antes que ele é enviado para o serviço Application Insights.

* [Amostragem](../../azure-monitor/app/sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Ela mantém em conjunto relacionado de pontos de dados para que pode navegar entre elas ao diagnosticar o problema. No portal, as contagens de total são multiplicadas para compensar a amostragem.
* Filtragem com processadores de telemetria [para o ASP.NET](#filtering) ou [Java](../../azure-monitor/app/java-filter-telemetry.md) permite selecionar ou modificar a telemetria no SDK antes de ser enviada para o servidor. Por exemplo, poderia reduzir o volume de telemetria ao excluir os pedidos de robôs. Mas a filtragem é uma abordagem mais básica para reduzir o tráfego de amostragem. Permite-lhe mais controlo sobre o que é transmitido, mas precisa estar ciente de que ele afeta as estatísticas - por exemplo, se filtrar todos os pedidos com êxito.
* [Inicializadores de telemetria adicionar propriedades](#add-properties) para qualquer telemetria enviada pela sua aplicação, incluindo telemetria a partir de módulos padrão. Por exemplo, poderia adicionar valores calculados; ou números de versão através do qual pretende filtrar os dados no portal.
* [A API do SDK](../../azure-monitor/app/api-custom-events-metrics.md) é utilizada para enviar eventos personalizados e métricas.

Antes de começar:

* Instale o Application Insights [SDK para ASP.NET](../../azure-monitor/app/asp-net.md) ou [SDK para Java](../../azure-monitor/app/java-get-started.md) na sua aplicação.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtragem: ITelemetryProcessor
Essa técnica oferece controle mais direto sobre o que é incluído ou excluído do fluxo de telemetria. Pode usá-lo em conjunto com amostragem, ou em separado.

Para filtrar a telemetria, escrever um processador de telemetria e registá-lo com o SDK. Toda a telemetria passa pelo seu processador e pode optar por soltá-la a partir do fluxo ou adicionar propriedades. Isto inclui a telemetria a partir de módulos padrão, como o coletor de pedido HTTP e o recoletor de dependência, bem como telemetria que ter escrito por. Pode, por exemplo, filtrar telemetria sobre pedidos de robôs ou chamadas de dependência com êxito.

> [!WARNING]
> Filtrar a telemetria enviada do SDK usar processadores pode distorcer as estatísticas que vê no portal do e dificultar a seguir itens relacionados.
>
> Em vez disso, considere a utilização [amostragem](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Criar um processador de telemetria (c#)
1. Certifique-se de que o SDK do Application Insights no seu projeto é a versão 2.0.0 ou posterior. Com o botão direito do rato no Explorador de soluções do Visual Studio e selecione gerir pacotes NuGet. No Gestor de pacotes do NuGet, verifique o applicationinsights.
2. Para criar um filtro, implemente ITelemetryProcessor. Este é outro ponto de extensibilidade, como o módulo de telemetria, o inicializador de telemetria e o canal de telemetria.

    Tenha em atenção que os processadores de telemetria construir uma cadeia de processamento. Quando criar uma instância de um processador de telemetria, passa um link para o processador seguinte na cadeia. Quando um ponto de dados de telemetria é passado para o método Process, ele executa o seu trabalho e, em seguida, chama o seguinte processador de telemetria na cadeia.

    ```csharp

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
3. Insira-o no applicationinsights. config:

```xml

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Esta é a mesma seção onde inicializar um filtro de amostragem.)

Pode passar valores de cadeia de caracteres do arquivo. config, fornecendo propriedades nomeadas públicas em sua classe.

> [!WARNING]
> Tenha cuidado de acordo com o nome do tipo e nenhum nome de propriedade no arquivo. config para os nomes de classe e propriedade no código. Se o arquivo. config fizer referência a um tipo não existente ou uma propriedade, o SDK pode falhar silenciosamente a enviar telemetria.
>
>

**Em alternativa,** pode inicializar o filtro no código. Numa classe de inicialização adequado - por exemplo AppStart em Global.asax.cs - Insira seu processador na cadeia de:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients criado a partir deste momento irá utilizar os processadores.

### <a name="example-filters"></a>Filtros de exemplo
#### <a name="synthetic-requests"></a>Pedidos sintéticos
Filtre os testes de bots e web. Embora o Explorador de métricas lhe dá a opção para filtrar os sintéticas origens, esta opção reduz o tráfego ao filtrá-los com o SDK.

```csharp

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Falha de autenticação
Filtre pedidos com uma resposta "401".

```csharp

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar chamadas de dependência rápida remoto
Se pretender apenas diagnosticar chamadas forem lentas, filtre aqueles fast.

> [!NOTE]
> Isso será distorcer as estatísticas que vê no portal. O gráfico de dependência parecerá como se as chamadas de dependência são todas as falhas.
>
>

```csharp

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência
[Este blogue](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência ao enviar automaticamente regulares pings para as dependências.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Adicione propriedades: ITelemetryInitializer
Inicializadores de telemetria de utilização para definir propriedades globais que são enviadas com toda a telemetria; e para substituir o comportamento selecionado dos módulos de telemetria standard.

Por exemplo, o Application Insights para o pacote Web recolhe telemetria sobre pedidos de HTTP. Por padrão, ele sinaliza como falhado qualquer pedido com um código de resposta > = 400. Mas se deseja tratar 400 como um êxito, pode fornecer um inicializador de telemetria que define a propriedade de sucesso.

Se fornecer um inicializador de telemetria, ele é chamado sempre que qualquer um dos métodos Track*() são chamados. Isto inclui métodos chamados por módulos de telemetria standard. Por convenção, estes módulos não defina qualquer propriedade que já foi definida por um inicializador.

**Definir seu inicializador**

*C#*

```csharp

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Carregar seu inicializador**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

*Em alternativa,* pode instanciar o inicializador no código, por exemplo, no Global.aspx.cs:

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Ver mais deste exemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="java-telemetry-initializers"></a>Inicializadores de telemetria de Java

[Documentação do SDK de Java](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Em seguida, registe o inicializador de personalizado no seu ficheiro applicationinsights XML.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
<Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria de JavaScript
*JavaScript*

Insira um inicializador de telemetria imediatamente depois do código de inicialização que recebeu do portal:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item�s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para obter um resumo das propriedades disponíveis sobre o telemetryItem não personalizado, consulte [modelo de dados exportação do Application Insights](../../azure-monitor/app/export-data-model.md).

Pode adicionar inicializadores de tantas quanto quiser.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer
O que é a diferença entre os processadores de telemetria e inicializadores de telemetria?

* Existem alguns sobreposições em que pode fazer com eles: podem ser utilizados para adicionar propriedades a telemetria.
* TelemetryInitializers sempre executado antes de TelemetryProcessors.
* TelemetryProcessors permitem-lhe substituir completamente ou eliminar um item de telemetria.
* TelemetryProcessors não processar a telemetria de contador de desempenho.

## <a name="troubleshooting-applicationinsightsconfig"></a>Resolução de problemas de applicationinsights. config
* Confirme que o nome do tipo completamente qualificado e o nome da assemblagem estão corretos.
* Confirme que o ficheiro applicationinsights. config está no seu diretório de saída e contém alterações recentes.

## <a name="reference-docs"></a>Documentos de referência
* [Descrição Geral da API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Código do SDK
* [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK DO ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Passos seguintes
* [Pesquisar eventos e registos](../../azure-monitor/app/diagnostic-search.md)
* [Amostragem](../../azure-monitor/app/sampling.md)
* [Resolução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
