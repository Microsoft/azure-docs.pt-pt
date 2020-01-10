---
title: Agregação de eventos de Service Fabric do Azure com EventFlow
description: Saiba mais sobre como agregar e coletar eventos usando o EventFlow para monitoramento e diagnóstico de clusters de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463090"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregação e coleta de eventos usando EventFlow

[O Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) pode rotear eventos de um nó para um ou mais destinos de monitoramento. Como ele é incluído como um pacote NuGet em seu projeto de serviço, o código EventFlow e a configuração viajam com o serviço, eliminando o problema de configuração por nó mencionado anteriormente sobre Diagnóstico do Azure. O EventFlow é executado em seu processo de serviço e se conecta diretamente às saídas configuradas. Devido à conexão direta, o EventFlow funciona para implantações do Azure, do contêiner e do serviço local. Tenha cuidado se você executar o EventFlow em cenários de alta densidade, como em um contêiner, porque cada pipeline EventFlow faz uma conexão externa. Portanto, se você hospedar vários processos, obterá várias conexões de saída! Isso não é tanto uma preocupação para Service Fabric aplicativos, porque todas as réplicas de um `ServiceType` são executadas no mesmo processo, e isso limita o número de conexões de saída. O EventFlow também oferece filtragem de eventos, para que somente os eventos que correspondem ao filtro especificado sejam enviados.

## <a name="set-up-eventflow"></a>Configurar o EventFlow

Os binários EventFlow estão disponíveis como um conjunto de pacotes NuGet. Para adicionar o EventFlow a um projeto de serviço Service Fabric, clique com o botão direito do mouse no projeto na Gerenciador de Soluções e escolha "gerenciar pacotes NuGet". Alterne para a guia "procurar" e pesquise "`Diagnostics.EventFlow`":

![Pacotes NuGet do EventFlow na interface do usuário do Gerenciador de pacotes NuGet do Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Você verá uma lista de vários pacotes mostrada, rotulada com "entradas" e "saídas". O EventFlow dá suporte a vários provedores de log e analisadores diferentes. O serviço que hospeda o EventFlow deve incluir os pacotes apropriados dependendo da origem e do destino dos logs do aplicativo. Além do pacote do principal Fabric, você também precisa de pelo menos uma entrada e saída configuradas. Por exemplo, você pode adicionar os seguintes pacotes para enviar eventos EventSource para Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` capturar dados da classe EventSource do serviço e de EventSources padrão como *Microsoft-Service Fabric-Services* e *Microsoft-Service Fabric-atores*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (vamos enviar os logs para um recurso de informações de Aplicativo Azure)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(habilita a inicialização do pipeline EventFlow de Service Fabric configuração do serviço e relata quaisquer problemas com o envio de dados de diagnóstico como Service Fabric relatórios de integridade)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` pacote exige que o projeto de serviço seja direcionado .NET Framework 4,6 ou mais recente. Certifique-se de definir a estrutura de destino apropriada nas propriedades do projeto antes de instalar este pacote.

Depois que todos os pacotes são instalados, a próxima etapa é configurar e habilitar EventFlow no serviço.

## <a name="configure-and-enable-log-collection"></a>Configurar e habilitar a coleta de log
O pipeline EventFlow responsável pelo envio dos logs é criado a partir de uma especificação armazenada em um arquivo de configuração. O pacote de `Microsoft.Diagnostics.EventFlow.ServiceFabric` instala um arquivo de configuração inicial do EventFlow em `PackageRoot\Config` pasta de solução, chamada `eventFlowConfig.json`. Esse arquivo de configuração precisa ser modificado para capturar dados da classe de `EventSource` de serviço padrão e quaisquer outras entradas que você queira configurar e enviar dados para o local apropriado.

>[!NOTE]
>Se o arquivo de projeto tiver o formato VisualStudio 2017, o arquivo de `eventFlowConfig.json` não será adicionado automaticamente. Para corrigir isso, crie o arquivo na pasta `Config` e defina a ação de Build como `Copy if newer`. 

Aqui está um exemplo de *eventFlowConfig. JSON* com base nos pacotes NuGet mencionados acima:
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

O nome do ServiceName do serviço é o valor da propriedade Name do `EventSourceAttribute` aplicado à classe messageeventname. Ele é especificado no arquivo `ServiceEventSource.cs`, que faz parte do código do serviço. Por exemplo, no trecho de código a seguir, o nome do ServiceName é *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Observe que `eventFlowConfig.json` arquivo faz parte do pacote de configuração de serviço. As alterações nesse arquivo podem ser incluídas em atualizações completas ou somente de configuração do serviço, sujeitas a Service Fabric verificações de integridade de atualização e reversão automática se houver falha de atualização. Para obter mais informações, consulte [Service Fabric atualização do aplicativo](service-fabric-application-upgrade.md).

A seção *filtros* da configuração permite que você personalize ainda mais as informações que vão passar pelo pipeline EventFlow para as saídas, permitindo que você remova ou inclua determinadas informações ou altere a estrutura dos dados do evento. Para obter mais informações sobre filtragem, consulte [EventFlow Filters](https://github.com/Azure/diagnostics-eventflow#filters).

A etapa final é instanciar o pipeline EventFlow no código de inicialização do serviço, localizado em `Program.cs` arquivo:

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

O nome passado como o parâmetro do método `CreatePipeline` da `ServiceFabricDiagnosticsPipelineFactory` é o nome da *entidade de integridade* que representa o pipeline de coleta de logs EventFlow. Esse nome será usado se o EventFlow encontrar e reportar o problema por meio do subsistema de integridade Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Usar configurações de Service Fabric e parâmetros de aplicativo em eventFlowConfig

O EventFlow dá suporte ao uso de configurações de Service Fabric e parâmetros de aplicativo para definir as configurações de EventFlow. Você pode consultar Service Fabric parâmetros de configurações usando essa sintaxe especial para valores:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` é o nome da seção de configuração de Service Fabric e `<setting-name>` é a definição de configuração que fornece o valor que será usado para definir uma configuração de EventFlow. Para ler mais sobre como fazer isso, acesse [suporte para configurações de Service Fabric e parâmetros de aplicativo](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verificação

Inicie o serviço e observe a janela de saída de depuração no Visual Studio. Depois que o serviço for iniciado, você deverá começar a ver as evidências de que o serviço está enviando registros para a saída que você configurou. Navegue até sua plataforma de análise e visualização de eventos e confirme se os logs começaram a aparecer (pode levar alguns minutos).

## <a name="next-steps"></a>Passos seguintes

* [Análise e visualização de eventos com Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise de eventos e visualização com logs de Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Documentação do EventFlow](https://github.com/Azure/diagnostics-eventflow)
