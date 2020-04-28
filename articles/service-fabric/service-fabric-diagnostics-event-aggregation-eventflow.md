---
title: Agregação de eventos de tecido de serviço Azure com EventFlow
description: Saiba agregação e recolha de eventos utilizando o EventFlow para monitorização e diagnóstico de clusters de tecido de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75463090"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregação e recolha de eventos usando eventFlow

O [Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) pode encaminhar eventos de um nó para um ou mais destinos de monitorização. Como está incluído como um pacote NuGet no seu projeto de serviço, código EventFlow e viagem de configuração com o serviço, eliminando o problema de configuração por nó mencionado anteriormente sobre o Azure Diagnostics. O EventFlow funciona dentro do seu processo de serviço e liga-se diretamente às saídas configuradas. Devido à ligação direta, o EventFlow trabalha para implantações de serviços Azure, contentores e no local. Tenha cuidado se executar o EventFlow em cenários de alta densidade, como num recipiente, porque cada gasoduto EventFlow faz uma ligação externa. Então, se você acolher vários processos, você obtém várias ligações de saída! Isto não é tanto uma preocupação para aplicações de `ServiceType` Tecido de Serviço, porque todas as réplicas de uma execução no mesmo processo, e isso limita o número de ligações de saída. O EventFlow também oferece filtragem de eventos, para que apenas sejam enviados os eventos que correspondam ao filtro especificado.

## <a name="set-up-eventflow"></a>Configurar o EventFlow

Os binários EventFlow estão disponíveis como um conjunto de pacotes NuGet. Para adicionar o EventFlow a um projeto de serviço de tecido de serviço, clique no projeto no Solution Explorer e escolha "Gerir pacotes NuGet". Mude para o separador "Navegar"`Diagnostics.EventFlow`e procure por " ":

![Pacotes EventFlow NuGet no Visual Studio NuGet package manager UI](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Você verá uma lista de vários pacotes aparecer, rotulados com "Inputs" e "Outputs". O EventFlow suporta vários fornecedores e analisadores de registo diferentes. O serviço que acolhe o EventFlow deve incluir pacotes apropriados dependendo da fonte e destino dos registos de aplicações. Além do pacote core ServiceFabric, também precisa de pelo menos uma Entrada e Saída configurada. Por exemplo, pode adicionar os seguintes pacotes para enviar eventos EventSource para Insights de Aplicação:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`para capturar dados da classe EventSource do serviço, e de fontes de eventos padrão, tais como *Microsoft-ServiceFabric-Services* e *Microsoft-ServiceFabric-Actors)*
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`(vamos enviar os registos para um recurso Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(permite a inicialização do pipeline EventFlow a partir da configuração do serviço Service Fabric e reporta quaisquer problemas com o envio de dados de diagnóstico como relatórios de saúde do Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource`o pacote requer que o projeto de serviço se desloque ao Quadro 4.6 ou mais recente. Certifique-se de que define o enquadramento-alvo adequado nas propriedades do projeto antes de instalar este pacote.

Depois de todas as embalagens estarem instaladas, o próximo passo é configurar e ativar o EventFlow no serviço.

## <a name="configure-and-enable-log-collection"></a>Configure e ativar a recolha de registos
O pipeline EventFlow responsável pelo envio dos registos é criado a partir de uma especificação armazenada num ficheiro de configuração. A `Microsoft.Diagnostics.EventFlow.ServiceFabric` embalagem instala um ficheiro de `PackageRoot\Config` configuração de `eventFlowConfig.json`EventoFlow inicial sob pasta de solução, nomeado . Este ficheiro de configuração precisa de ser `EventSource` modificado para capturar dados da classe de serviço predefinido, e quaisquer outras inputs que queira configurar, e enviar dados para o local apropriado.

>[!NOTE]
>Se o seu ficheiro do projeto tiver `eventFlowConfig.json` formato VisualStudio 2017, o ficheiro não será adicionado automaticamente. Para corrigir isto crie `Config` o ficheiro na `Copy if newer`pasta e defina a ação de construção para . 

Aqui está um evento de *amostraFlowConfig.json* com base nos pacotes NuGet mencionados acima:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

O nome do ServiceEventSource do serviço é o `EventSourceAttribute` valor da propriedade Nome da aplicação à classe ServiceEventSource. Tudo está especificado `ServiceEventSource.cs` no ficheiro, que faz parte do código de serviço. Por exemplo, no seguinte código, o nome do ServiceEventSource é *MyCompany-Application1-Stateless1:*

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Note `eventFlowConfig.json` que o ficheiro faz parte do pacote de configuração do serviço. As alterações a este ficheiro podem ser incluídas em atualizações completas ou de configuração do serviço, sujeitas a verificações de saúde de upgrade do Service Fabric e reversão automática se houver falha de atualização. Para mais informações, consulte a [atualização da aplicação Service Fabric](service-fabric-application-upgrade.md).

A secção de *filtros* da config permite-lhe personalizar ainda mais a informação que vai passar pelo pipeline EventFlow para as saídas, permitindo-lhe deixar cair ou incluir determinadas informações, ou alterar a estrutura dos dados do evento. Para obter mais informações sobre filtragem, consulte [os filtros EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

O passo final é instantaneamente o pipeline EventFlow no `Program.cs` código de arranque do seu serviço, localizado em ficheiro:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

O nome passou como parâmetro `CreatePipeline` do `ServiceFabricDiagnosticsPipelineFactory` método do é o nome da *entidade* sanitária que representa o pipeline de recolha de log Flow EventFlow. Este nome é usado se o EventFlow se encontrar e errar e reportá-lo através do subsistema de saúde Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Utilizar definições de tecido de serviço e parâmetros de aplicação em eventoSFlowConfig

O EventFlow suporta a utilização de definições de tecido de serviço e parâmetros de aplicação para configurar as definições do EventFlow. Pode consultar os parâmetros de definições do Tecido de Serviço utilizando esta sintaxe especial para valores:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`é o nome da secção de `<setting-name>` configuração do Tecido de Serviço, e é a configuração de configuração que fornece o valor que será usado para configurar uma definição de EventFlow. Para ler mais sobre como fazê-lo, vá ao [Suporte para Definições de Tecido](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters)de Serviço e parâmetros de aplicação .

## <a name="verification"></a>Verificação

Inicie o seu serviço e observe a janela de saída do Debug no Estúdio Visual. Depois de iniciado o serviço, deve começar a ver provas de que o seu serviço está a enviar registos para a saída que configura. Navegue para a sua plataforma de análise e visualização de eventos e confirme que os registos começaram a aparecer (pode demorar alguns minutos).

## <a name="next-steps"></a>Passos seguintes

* [Análise de Eventos e Visualização com Insights de Aplicação](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise e Visualização de Eventos com registos do Monitor Azure](service-fabric-diagnostics-event-analysis-oms.md)
* [Documentação EventFlow](https://github.com/Azure/diagnostics-eventflow)
