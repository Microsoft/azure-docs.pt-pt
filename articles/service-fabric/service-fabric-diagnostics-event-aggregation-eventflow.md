---
title: Agregação de eventos de tecido de serviço Azure com EventFlow
description: Saiba mais sobre agregação e recolha de eventos utilizando o EventFlow para monitorização e diagnóstico de clusters de tecidos de serviço Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a80eea5a50aa7b1e441049eeb2cae381994cd3ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018659"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregação e recolha de eventos usando EventFlow

[O Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) pode encaminhar eventos de um nó para um ou mais destinos de monitorização. Uma vez que está incluído como um pacote NuGet no seu projeto de serviço, o código EventFlow e a configuração viajam com o serviço, eliminando o problema de configuração por nó mencionado anteriormente sobre o Azure Diagnostics. O EventFlow funciona dentro do seu processo de serviço e liga-se diretamente às saídas configuradas. Devido à ligação direta, o EventFlow trabalha para a Azure, contentores e implementações de serviço no local. Tenha cuidado se executar o EventFlow em cenários de alta densidade, como num recipiente, porque cada gasoduto EventFlow faz uma ligação externa. Por isso, se hospedar vários processos, obtém várias ligações de saída! Isto não é tanto uma preocupação para as aplicações do Service Fabric, porque todas as réplicas de uma `ServiceType` corrida no mesmo processo, e isso limita o número de ligações de saída. O EventFlow também oferece filtragem de eventos, para que apenas sejam enviados os eventos que correspondam ao filtro especificado.

## <a name="set-up-eventflow"></a>Configurar o EventFlow

Os binários EventFlow estão disponíveis como um conjunto de pacotes NuGet. Para adicionar o EventFlow a um projeto de serviço de Service Fabric, clique com o botão direito no projeto no Solution Explorer e escolha "Gerir pacotes NuGet". Mude para o separador "Procurar" e procure " `Diagnostics.EventFlow`

![Pacotes EventFlow NuGet em Visual Studio NuGet gestor de pacotes UI](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Você verá uma lista de vários pacotes aparecem, rotulados com "Inputs" e "Outputs". O EventFlow suporta vários fornecedores e analisadores de registos diferentes. O eventFlow de hospedagem de serviço deve incluir pacotes apropriados dependendo da origem e destino para os registos de aplicações. Além do pacote Core ServiceFabric, também precisa de pelo menos uma entrada e saída configuradas. Por exemplo, pode adicionar os seguintes pacotes para enviar eventos EventSource para Insights de Aplicação:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` para capturar dados da classe EventSource do serviço, e de EventSources padrão como *Microsoft-ServiceFabric-Services* e *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (vamos enviar os registos para um recurso Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(permite a inicialização do gasoduto EventFlow a partir da configuração do serviço service Fabric e reporta quaisquer problemas com o envio de dados de diagnóstico como relatórios de saúde do Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` pacote requer que o projeto de serviço se desponte para o Quadro .NET 4.6 ou mais recente. Certifique-se de que define o quadro-alvo adequado nas propriedades do projeto antes de instalar este pacote.

Depois de todos os pacotes estarem instalados, o próximo passo é configurar e ativar o EventFlow no serviço.

## <a name="configure-and-enable-log-collection"></a>Configure e permita a recolha de registos
O pipeline EventFlow responsável pelo envio dos registos é criado a partir de uma especificação armazenada num ficheiro de configuração. O `Microsoft.Diagnostics.EventFlow.ServiceFabric` pacote instala um ficheiro de configuração EventFlow inicial sob a pasta de `PackageRoot\Config` solução, denominado `eventFlowConfig.json` . Este ficheiro de configuração precisa de ser modificado para capturar dados da classe de serviço `EventSource` predefinida, e quaisquer outras entradas que pretenda configurar e enviar dados para o local apropriado.

>[!NOTE]
>Se o seu ficheiro de projeto tiver formato VisualStudio 2017, o `eventFlowConfig.json` ficheiro não será automaticamente adicionado. Para corrigir isto, crie o ficheiro na `Config` pasta e desemobre a ação de construção para `Copy if newer` . 

Aqui está uma amostra *eventFlowConfig.js* com base nos pacotes NuGet mencionados acima:
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

O nome do ServiceEventSource é o valor da propriedade Name do `EventSourceAttribute` aplicado à classe ServiceEventSource. Está tudo especificado no `ServiceEventSource.cs` ficheiro, que faz parte do código de serviço. Por exemplo, no seguinte código, o nome do ServiceEventSource é *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Note que o `eventFlowConfig.json` ficheiro faz parte do pacote de configuração de serviço. As alterações a este ficheiro podem ser incluídas em atualizações completas ou apenas de configuração do serviço, sujeitas a verificações de saúde de upgrade do Service Fabric e reversão automática se houver falha na atualização. Para mais informações, consulte [a atualização da aplicação Service Fabric](service-fabric-application-upgrade.md).

A secção de *filtros* do config permite-lhe personalizar ainda mais as informações que vão passar pelo pipeline EventFlow para as saídas, permitindo-lhe deixar cair ou incluir determinadas informações, ou alterar a estrutura dos dados do evento. Para obter mais informações sobre a filtragem, consulte [os filtros EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

O passo final é instantaneamente o pipeline EventFlow no código de arranque do seu serviço, localizado em `Program.cs` ficheiro:

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

O nome passado como parâmetro do `CreatePipeline` método do é o nome da `ServiceFabricDiagnosticsPipelineFactory` *entidade* sanitária que representa o pipeline de recolha de logs EventFlow. Este nome é utilizado se o EventFlow encontrar e cometer erros e o reportar através do subsistema de saúde Do Tecido de Serviço.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Utilizar configurações de tecido de serviço e parâmetros de aplicação no eventoFlowConfig

O EventFlow suporta a utilização de configurações de Tecido de Serviço e parâmetros de aplicação para configurar as definições do EventFlow. Pode consultar os parâmetros de definição de Tecido de Serviço utilizando esta sintaxe especial para valores:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` é o nome da secção de configuração do Tecido de Serviço, e `<setting-name>` é a definição de configuração que fornece o valor que será usado para configurar uma definição eventFlow. Para ler mais sobre como fazê-lo, vá ao [Suporte para Configurações de Tecido de Serviço e parâmetros de aplicação](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verificação

Inicie o seu serviço e observe a janela de saída Debug no Visual Studio. Após o início do serviço, deverá começar a ver provas de que o seu serviço está a enviar registos para a saída que configuraram. Navegue para a sua plataforma de análise e visualização de eventos e confirme que os registos começaram a aparecer (pode demorar alguns minutos).

## <a name="next-steps"></a>Passos seguintes

* [Análise e Visualização de Eventos com Insights de Aplicações](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise e Visualização de Eventos com registos do Monitor Azure](service-fabric-diagnostics-event-analysis-oms.md)
* [Documentação EventFlow](https://github.com/Azure/diagnostics-eventflow)
