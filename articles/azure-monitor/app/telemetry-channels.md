---
title: Canais de telemetria no Application Insights do Azure | Documentos da Microsoft
description: Como personalizar os canais de telemetria no Azure SDKs do Application Insights para núcleo.NET/.NET.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255811"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel no Application Insights

TelemetryChannel é uma parte integral da [SDKs do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Ele gerencia o armazenamento em buffer e transmissão de telemetria para o serviço do Application Insights. As versões de .NET e .NET Core dos SDKs têm dois TelemetryChannels incorporadas - `InMemoryChannel` e `ServerTelemetryChannel`. Este artigo descreve cada canal em detalhes, incluindo a forma como os usuários podem personalizar o comportamento de canal.

## <a name="what-is-a-telemetrychannel"></a>O que é um TelemetryChannel?

`TelemetryChannel` é responsável por armazenamento em buffer e enviar itens de telemetria para o serviço do Application Insights, onde são armazenados para consulta e análise. É qualquer classe de implementação da interface [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

O `Send(ITelemetry item)` método de TelemetryChannel é chamado Afinal de contas `TelemetryInitializer`s e `TelemetryProcessor`s são chamados. Isso significa que todos os itens por `TelemetryProcessor` não atingirá o canal. `Send()` Não, normalmente, envia os itens instantaneamente para o back-end. Eles são normalmente em buffer na memória e enviadas em lotes, para transmissão eficiente.

[LiveMetrics](live-stream.md) também tem um canal personalizado, o que ativa a transmissão em direto de telemetria. Este canal é independente do canal telemetria regular e, neste documento não é aplicável para o canal utilizado pelo `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>TelemetryChannels incorporada

Application Insights.NET/.NET Core SDK é fornecido com dois canais incorporadas:

* **InMemoryChannel** 
 `InMemoryChannel` é um canal de leves, o que provoca itens na memória até que seja enviada. Itens são colocados em memória intermédia na memória e o vez a cada 30 segundos ou sempre que tiveram armazenado em buffer 500 itens. Este canal oferece garantias de confiabilidade mínima uma vez que ele não repetir o envio de telemetria após falhas. Este canal não manter itens no disco, para que todos os itens não enviados são perdidos permanentemente após o encerramento do aplicativo (corretamente ou não). Existe um `Flush()` método implementado por este canal, que pode ser utilizado para força-flush quaisquer itens de telemetria de dentro da memória forma síncrona. Isso é ideal para aplicativos de curta execução onde um esvaziamento síncrono é ideal.

    Este canal é enviado como parte do `Microsoft.ApplicationInsights` nuget do pacote em si e é o canal predefinido que o SDK utiliza quando nada mais é configurado.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` é um canal mais avançado, que tem as políticas de repetição e a capacidade de armazenar dados no disco local. Este canal repete o envio de telemetria, se ocorrerem erros transitórios. Este canal também utiliza o armazenamento de disco local para manter os itens no disco durante as falhas de rede ou volumes de telemetria elevada. Devido a estes mecanismos de repetição e o armazenamento de disco local, este canal é considerado mais confiável e é recomendado para todos os cenários de produção. Este canal é a predefinição para [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplicativos, que estão configurados de acordo com os documentos oficiais ligados. Este canal é otimizado para cenários de servidor de processos de longa execução. O [ `Flush()` ](#which-channel-should-i-use) método implementado através deste canal não é síncrono.

    Este canal é enviado como o pacote NuGet `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`e ser colocado automaticamente ao utilizar qualquer um dos pacotes de NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>Configurar TelemetryChannel

Canal de telemetria pode ser configurada através da definição desejada `TelemetryChannel` sobre o Active Directory `TelemetryConfiguration`. Para aplicativos Asp.Net, a configuração envolve a definição `TelemetryChannel` nos `TelemetryConfiguration.Active`, ou modificar `ApplicationInsights.config`. Para aplicativos do ASP.NET Core, a configuração envolve a adição do canal pretendido para o contêiner de Injeção de dependência.

Seguinte mostra um exemplo em que está a configurar o utilizador a `StorageFolder` para o canal. `StorageFolder` é apenas uma das definições configuráveis. A lista completa das definições de configuração está descrita [da secção definições da](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração com o applicationinsights. config para aplicativos ASP.NET

A seguinte secção partir [applicationinsights. config](configuration-with-applicationinsights-config.md) mostra ServerTelemetryChannel configurado com `StorageFolder` definido para uma localização personalizada.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Configuração no código para aplicativos ASP.NET

O código a seguir configura ServerTelemetryChannel com `StorageFolder` definido para uma localização personalizada. Esse código deve ser adicionado no início do aplicativo, normalmente no método application_start () na `Global.aspx.cs`

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuração no código para aplicativos do ASP.NET Core

Modificar `ConfigureServices` método de `Startup.cs` classe conforme mostrado abaixo.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!NOTE]
> É importante observar que configurar o canal usando `TelemetryConfiguration.Active` não é recomendada para aplicações ASP.NET Core.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Configurar TelemetryChannel no código para aplicativos de Console do.NET/.NET principais

Para aplicativos de Console, o código é a mesmo para .NET e .NET Core e é mostrado abaixo.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais de ServerTelemetryChannel

O `ServerTelemetryChannel` coloca na memória intermédia itens que são recebidos num buffer na memória. Ele é serializado, comprimir e armazenar em `Transmission` uma vez a cada 30 segundos ou quando 500 itens são colocados em memória intermédia de instância. Um único `Transmission` contém até 500 itens a instância e representa um lote de telemetria que está a ser enviado através de uma chamada única https para o serviço do Application Insights. Por predefinição, pode haver um máximo de 10 `Transmission`s que está a ser enviadas em paralelo. Se a telemetria está sendo recebido com taxas mais rápidas, ou se o back-end de rede/Application Insights está lento, em seguida, `Transmission`s são armazenados na memória. A capacidade predefinida desse buffer de transmissão de dentro da memória é 5 MB. Assim que excede a capacidade de dentro da memória, `Transmission`s são armazenados no disco local para 50 MB. `Transmission`s são armazenados no disco local quando existem problemas de rede também. Apenas os itens armazenados no disco local sobrevivem a uma falha de aplicativo, que é enviado sempre que o aplicativo é iniciado novamente.

## <a name="configurable-settings-in-channel"></a>Definições configuráveis no canal

A lista completa de definições configuráveis para cada canal são aqui:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Mais comumente usadas as definições para `ServerTelemetryChannel` estão listados abaixo:

1. `MaxTransmissionBufferCapacity` -Máximo de memória, em bytes, usado pelo canal para as transmissões de buffer na memória. Assim que esta capacidade for atingida, novos itens serão armazenados diretamente para o disco local. A predefinição é 5 MB. Definir um valor mais alto oportunidades potenciais à utilização do disco menor, mas é importante observar que os itens na memória serão perdidos no caso de falha de aplicação.

2. `MaxTransmissionSenderCapacity` -Máximo de `Transmission`s que serão enviadas para o Application Insights ao mesmo tempo. A predefinição é 10, mas ele pode ser configurado para um número mais elevado. Isto é recomendado quando é gerado um grande volume de telemetria, normalmente, quando fazer testes de carga e/ou quando a amostragem está desativada.

3. `StorageFolder` -A pasta utilizada pelo canal para armazenar itens em disco conforme necessário. No Windows, % LocalAppData % ou % Temp % é utilizado, se nada estiver configurado explicitamente. Em ambientes de não-Windows, usuário **tem** configure uma localização válida, sem que a telemetria não será armazenada no disco local.

## <a name="which-channel-should-i-use"></a>Que canal devo utilizar?

`ServerTelemetryChannel` Recomenda-se na maioria dos cenários de produção de aplicativos de longa execução. O `Flush()` implementação do método de `ServerTelemetryChannel` não é síncrona, e `Flush()` não garante a enviar de todos os itens pendentes de memória/disco. Se este canal é usado em cenários em que o aplicativo está prestes a encerrar, em seguida, é recomendável fazer algum atraso após chamar `Flush()` neste canal. A quantidade exata de atraso necessário não for previsível, pois depende de fatores, como quantos itens ou `Transmissions` estão na memória, quantas têm no disco, quantos estão a ser transmitidos para uma segurança, e se o canal permanece no meio de cenários de término exponenciais. Se for necessário fazer flush síncrona, em seguida, `InMemoryChannel` é recomendado.

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Canal do Application Insights oferece entrega garantida telemetria ou quais são os cenários em que a telemetria pode ser perdida?*

* Resposta abreviada é que nenhum dos canais internos oferecem garantia de tipo de transação sobre a entrega de telemetria para o back-end. Embora `ServerTelemetryChannel` é mais avançada em comparação com `InMemoryChannel` para uma entrega fiável de telemetria, também faz uma tentativa de melhor esforço para enviar a telemetria e telemetria ainda pode ser perdida em vários cenários. Alguns dos cenários comuns em que a telemetria é perdida incluem:

1. Itens na memória perdem-se sempre que o aplicativo falha.
1. Telemetria é armazenada no disco local durante falhas de rede ou problemas com o back-end do Application Insights. No entanto, os itens com mais de 24 horas são eliminadas. Portanto, a telemetria é perdida durante um período prolongado de problemas de rede.
1. As localizações de disco predefinido para armazenar a telemetria no Windows são % LocalAppData % ou % Temp %. Estas localizações estão normalmente locais do computador. Se o aplicativo fisicamente migrada de um único local para outro, qualquer telemetria armazenada nesta localização é perdida.
1. Nas aplicações Web do Azure (Windows), a localização do disco predefinido é "D:\local\LocalAppData". Esta localização não é mantida e serem eliminada em reinícios da aplicação, o contras do dimensionamento e assim por diante, levando a perda de telemetria armazenada nesses locais. Os utilizadores podem substituir o armazenamento a uma localização persistente como "D:\home", mas esses locais persistentes abaixo são servidos pelo armazenamento remoto e podem ser lentos.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel funciona em sistemas de não-Windows?*

* Apesar do nome do pacote/espaço de nomes que está a ser WindowsServer, este canal é suportado em sistemas de não-Windows com a seguinte exceção. Não-Windows, o canal não cria uma pasta de armazenamento local por predefinição. Os utilizadores tem de criar uma pasta de armazenamento local e configurar o canal para utilizá-lo. Assim que o armazenamento local está configurado, o canal funciona mesmo no Windows e sistemas de não-Windows.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*O SDK cria um armazenamento temporário local? Os dados são encriptados no armazenamento?*

* SDK armazena itens de telemetria no armazenamento local durante a problemas de rede ou durante a limitação. Estes dados não são encriptados localmente.
Para sistemas Windows, o SDK automaticamente cria uma pasta local temporária no diretório TEMP ou APPDATA e restringe o acesso aos administradores e apenas o utilizador atual.
Para não-Windows, sem armazenamento local é criado automaticamente pelo SDK e, por conseguinte, nenhum dado é armazenado localmente por predefinição. Os utilizadores podem criar um diretório de armazenamento próprios e configurar o canal para utilizá-lo. Neste caso, o utilizador é responsável por assegurar que este diretório está protegido.
Leia mais sobre [proteção de dados e privacidade](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK de código-fonte aberto
Como todos os SDKs do Application Insights, os canais são também código-fonte aberto. Ler e contribuir para o código ou reportar problemas na [o repositório do GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Próximos Passos

* [Amostragem](../../azure-monitor/app/sampling.md)
* [Resolução de problemas de SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
