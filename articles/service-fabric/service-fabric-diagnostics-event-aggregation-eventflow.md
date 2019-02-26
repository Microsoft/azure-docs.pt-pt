---
title: Agregação de eventos de recursos de infraestrutura de serviço do Azure com o EventFlow | Documentos da Microsoft
description: Saiba mais sobre a agregação e a recolha de eventos com o EventFlow para monitorização e diagnóstico de clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 31d06c6329a8348a73533a42b73ce435c573c034
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823880"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregação de eventos e coleções com o EventFlow

[O EventFlow de diagnóstico do Microsoft](https://github.com/Azure/diagnostics-eventflow) podem encaminhar eventos a partir de um nó para um ou mais destinos de monitorização. Uma vez que está incluído como um pacote de NuGet no seu projeto de serviço, o EventFlow código e a configuração de deslocação com o serviço, eliminando o problema de configuração por nó, mencionado anteriormente sobre o diagnóstico do Azure. O EventFlow é executado dentro de seu processo de serviço e liga-se diretamente para as saídas configuradas. Devido a conexão direta, o EventFlow funciona para o Azure, contentores e implementações de serviço no local. Tenha cuidado se executar o EventFlow em cenários de alta densidade, tal como num contentor, porque cada pipeline o EventFlow faz com que uma ligação externa. Então, se alojar vários processos, obtém várias ligações de saída! Isso não é tanto uma preocupação para aplicações do Service Fabric, porque todas as réplicas de um `ServiceType` executar no mesmo processo, e isso limita o número de ligações de saída. O EventFlow também oferece filtragem de eventos, para que apenas os eventos que correspondem ao filtro especificado são enviados.

## <a name="set-up-eventflow"></a>Configurar o EventFlow

O EventFlow binários estão disponíveis como um conjunto de pacotes NuGet. Para adicionar o EventFlow para um projeto de serviço do Service Fabric, com o botão direito do rato no Explorador de soluções e escolha "Manage NuGet packages." Mude para o separador "Procurar", procure por "`Diagnostics.EventFlow`":

![Pacotes de NuGet o EventFlow no Gestor de pacotes NuGet do Visual Studio da interface do Usuário](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Verá uma lista de vários pacotes de mostrar, etiquetados com "Entradas" e "Saídas". O EventFlow oferece suporte a vários provedores de registro diferentes e os analisadores. O serviço que aloja o EventFlow deve incluir pacotes adequados, consoante a origem e destino para os registos da aplicação. Além do pacote do ServiceFabric de núcleos, também tem de, pelo menos, uma entrada e saída configurados. Por exemplo, pode adicionar os seguintes pacotes para enviar eventos de EventSource para o Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` para capturar os dados de classe de EventSource do serviço e de EventSources padrão, tal como *serviços Microsoft-ServiceFabric* e *Microsoft-ServiceFabric-Atores*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (vamos enviar os registos para um recurso do Application Insights do Azure)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(permite que a inicialização do pipeline o EventFlow da configuração de serviço do Service Fabric e relata quaisquer problemas com o envio de dados de diagnóstico como relatórios de estado de funcionamento do Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` pacote necessita que o projeto de serviço para visar o .NET Framework 4.6 ou mais recente. Certifique-se de que definir a estrutura de destino apropriada nas propriedades do projeto antes de instalar este pacote.

Após a instalação de todos os pacotes, a próxima etapa é configurar e ativar o EventFlow no serviço.

## <a name="configure-and-enable-log-collection"></a>Configurar e ativar a recolha de registos
O pipeline do EventFlow responsável por enviar os registos é criado a partir de uma especificação armazenada num ficheiro de configuração. O `Microsoft.Diagnostics.EventFlow.ServiceFabric` pacote instala um arquivo de configuração inicial do EventFlow sob `PackageRoot\Config` pasta de soluções, com o nome `eventFlowConfig.json`. Este ficheiro de configuração precisa ser modificado para capturar os dados do serviço predefinido `EventSource` classe e quaisquer outras entradas que pretende configurar e enviar dados para o local apropriado.

>[!NOTE]
>Se seu arquivo de projeto tem um formato de 2017 VisualStudio o `eventFlowConfig.json` ficheiro não serão automaticamente adicionado. Para corrigir isto criar o ficheiro no `Config` pasta e defina a ação de compilação para `Copy if newer`. 

Eis um exemplo *eventFlowConfig.json* com base nos pacotes de NuGet mencionados acima:
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

O nome ServiceEventSource do serviço é o valor da propriedade do nome do `EventSourceAttribute` aplicado à classe ServiceEventSource. Tudo é especificado no `ServiceEventSource.cs` arquivo, que é parte do código de serviço. Por exemplo, no seguinte fragmento de código o nome do ServiceEventSource é *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Tenha em atenção que `eventFlowConfig.json` ficheiro faz parte do pacote de configuração do serviço. Alterações a este ficheiro podem ser incluídas em atualizações de full - ou só de configuração do serviço, sujeitos a verificações de estado de funcionamento de atualização do Service Fabric e Reversão automática se ocorrer falha de atualização. Para obter mais informações, consulte [atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md).

O *filtros* secção da configuração permite-lhe ainda mais a personalizar as informações que vão passar pelo pipeline o EventFlow para as saídas, permitindo que remova ou incluir determinadas informações, ou alterar a estrutura das dados de eventos. Para obter mais informações sobre a filtragem, consulte [filtros o EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

A última etapa é instanciar o EventFlow pipeline no código de inicialização do seu serviço, localizado na `Program.cs` ficheiro:

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

O nome passado como o parâmetro do `CreatePipeline` método da `ServiceFabricDiagnosticsPipelineFactory` é o nome da *entidade de estado de funcionamento* que representa o pipeline de coleção de log o EventFlow. Este nome é utilizado se encontra o EventFlow e o erro e relata por meio do subsistema de estado de funcionamento do Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Utilize as definições do Service Fabric e os parâmetros de aplicação eventFlowConfig

O EventFlow suporta as definições do Service Fabric e os parâmetros de aplicação a utilizar para configurar as definições do EventFlow. Pode consultar parâmetros de definições do Service Fabric utilizando esta sintaxe especial para os valores de:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` é o nome da seção de configuração do Service Fabric, e `<setting-name>` é fornecer o valor que será utilizado para configurar uma definição do EventFlow a definição de configuração. Para ler mais sobre como fazê-lo, aceda a [suporte para as definições do Service Fabric e os parâmetros de aplicação](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verificação

Inicie o serviço e a depuração de observar a janela de saída no Visual Studio. Depois do serviço é iniciado, deve começar a ver evidências que seu serviço está a enviar registos para a saída que tiver configurado. Navegue até à sua plataforma de análise e visualização de eventos e confirmar que os registos de tem iniciado Mostrar verticalmente (pode demorar alguns minutos).

## <a name="next-steps"></a>Passos Seguintes

* [Análise de eventos e visualização com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise de eventos e visualização com o Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)
* [Documentação do EventFlow](https://github.com/Azure/diagnostics-eventflow)
