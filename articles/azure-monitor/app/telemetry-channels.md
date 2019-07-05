---
title: Canais de telemetria no Application Insights do Azure | Documentos da Microsoft
description: Como personalizar os canais de telemetria no Azure SDKs do Application Insights para o .NET e .NET Core.
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
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561351"
---
# <a name="telemetry-channels-in-application-insights"></a>Canais de telemetria no Application Insights

Os canais de telemetria são parte integrante dos [SDKs do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Eles gerenciam o armazenamento em buffer e transmissão de telemetria para o serviço do Application Insights. As versões de .NET e .NET Core dos SDKs têm dois canais de telemetria incorporada: `InMemoryChannel` e `ServerTelemetryChannel`. Este artigo descreve cada canal em detalhes, incluindo como personalizar o comportamento de canal.

## <a name="what-are-telemetry-channels"></a>O que são canais de telemetria?

Canais de telemetria são responsáveis por na memória intermédia de itens de telemetria e enviando-as para o serviço do Application Insights, onde estão armazenados para consulta e análise. Um canal de telemetria é qualquer classe que implementa a [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) interface.

O `Send(ITelemetry item)` método de um canal de telemetria é chamado após todos os inicializadores de telemetria e são chamados de processadores de telemetria. Então, todos os itens processados por um processador de telemetria não atingirá o canal. `Send()` não normalmente enviam os itens para o back-end instantaneamente. Normalmente, ele coloca na memória na memória intermédia-os e envia-os em lotes, para transmissão eficiente.

[Live Stream métricas](live-stream.md) também tem um canal personalizado que alimenta o streaming de telemetria em direto. Este canal é independente do canal telemetria regulares e, neste documento não se aplica ao mesmo.

## <a name="built-in-telemetry-channels"></a>Canais de telemetria incorporada

O Application Insights .NET e .NET Core SDKs são enviados juntamente com dois canais incorporadas:

* `InMemoryChannel`: Um canal leve que coloca na memória intermédia itens na memória até que são enviados. Itens são colocados em memória intermédia na memória e o vez a cada 30 segundos, ou sempre que estiverem armazenado em buffer 500 itens. Este canal oferece garantias de confiabilidade mínima porque ele não repetir o envio de telemetria após uma falha. Este canal também não manter itens no disco, para que todos os itens não enviados são perdidos permanentemente após o encerramento do aplicativo (normal ou não). Este canal implementa um `Flush()` método que pode ser usado para força-flush quaisquer itens de telemetria de dentro da memória forma síncrona. Este canal é ideal para aplicativos de curta execução onde um esvaziamento síncrono é ideal.

    Este canal faz parte do pacote NuGet do applicationinsights maior e é o canal predefinido que o SDK utiliza quando nada mais é configurado.

* `ServerTelemetryChannel`: Um canal mais avançado que tenha as políticas de repetição e a capacidade de armazenar dados num disco local. Este canal repete o envio de telemetria, se ocorrerem erros transitórios. Este canal também utiliza o armazenamento de disco local para manter os itens no disco durante as falhas de rede ou volumes de telemetria elevada. Devido a estes mecanismos de repetição e o armazenamento de disco local, este canal é considerado mais confiável e é recomendado para todos os cenários de produção. Este canal é a predefinição para [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplicativos que estão configurados de acordo com a documentação oficial. Este canal é otimizado para cenários de servidor com processos de longa execução. O [ `Flush()` ](#which-channel-should-i-use) método que é implementado por este canal não é síncrono.

    Este canal é enviado como o pacote Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet e é obtido automaticamente quando utiliza o applicationinsights ou Microsoft.ApplicationInsights.AspNetCore NuGet pacote.

## <a name="configure-a-telemetry-channel"></a>Configurar um canal de telemetria

Configurar um canal de telemetria ao defini-la para a configuração de telemetria do Active Directory. Para aplicativos ASP.NET, a configuração envolve a definição da instância de canal de telemetria `TelemetryConfiguration.Active`, ou ao modificar `ApplicationInsights.config`. Para aplicativos do ASP.NET Core, a configuração envolve a adição do canal para o contêiner de Injeção de dependência.

As secções seguintes mostram exemplos de configuração a `StorageFolder` definição para o canal em vários tipos de aplicação. `StorageFolder` é apenas uma das definições configuráveis. Para obter a lista completa das definições de configuração, consulte [da secção definições da](telemetry-channels.md#configurable-settings-in-channels) mais adiante neste artigo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração ao utilizar o applicationinsights. config para aplicativos ASP.NET

A seguinte secção partir [applicationinsights. config](configuration-with-applicationinsights-config.md) mostra o `ServerTelemetryChannel` canal configurado com `StorageFolder` definido para uma localização personalizada:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Configuração no código para aplicativos ASP.NET

O código a seguir configura uma instância de 'ServerTelemetryChannel' com `StorageFolder` definido para uma localização personalizada. Adicione este código no início do aplicativo, normalmente em `Application_Start()` método na Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuração no código para aplicativos do ASP.NET Core

Modificar a `ConfigureServices` método da `Startup.cs` classe conforme mostrado aqui:

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

> [!IMPORTANT]
> Configurar o canal usando `TelemetryConfiguration.Active` não é recomendada para aplicações ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuração no código para aplicativos de console.NET/.NET Core

Para aplicativos de console, o código é o mesmo para o .NET e .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais de ServerTelemetryChannel

`ServerTelemetryChannel` lojas que chegam itens num buffer na memória. Os itens são serializados, compactados e armazenados num `Transmission` a cada 30 segundos, de instância ou quando 500 itens tem sido colocados em memória intermédia. Um único `Transmission` instância contém até 500 itens e representa um lote de telemetria que enviou ao longo de uma única chamada HTTPS para o serviço Application Insights.

Por predefinição, um máximo de 10 `Transmission` instâncias podem ser enviadas em paralelo. Se a telemetria está sendo recebido com taxas mais rápidas, ou se a rede ou o Application Insights uma finais é lenta, `Transmission` instâncias são armazenadas na memória. A capacidade predefinida deste dentro da memória `Transmission` memória intermédia é 5 MB. Quando tiver sido excedida a capacidade de dentro da memória, `Transmission` instâncias são armazenadas no disco local até um limite de 50 MB. `Transmission` instâncias também são armazenadas no disco local quando existem problemas de rede. Apenas os itens que são armazenados num disco local sobrevivem a uma falha de aplicativo. Que são enviados sempre que o aplicativo é iniciado novamente.

## <a name="configurable-settings-in-channels"></a>Definições configuráveis em canais

Para obter a lista completa de definições configuráveis para cada canal, consulte:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Seguem-se as definições mais utilizadas para `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: A quantidade máxima de memória, em bytes, usado pelo canal para as transmissões de buffer na memória. Quando esta capacidade é alcançada, os novos itens são armazenados diretamente para o disco local. O valor predefinido é 5 MB. Definir um valor mais alto resulta em menos utilização do disco, mas lembre-se de que itens na memória serão perdidos se a falha da aplicação.

1. `MaxTransmissionSenderCapacity`: O número máximo de `Transmission` instâncias que serão enviadas para o Application Insights ao mesmo tempo. O valor predefinido é 10. Esta definição pode ser configurada para um número mais elevado, o que é recomendado quando é gerado um grande volume de telemetria. Alto volume normalmente ocorre durante o teste de carga ou quando a amostragem está desativada.

1. `StorageFolder`: A pasta que é utilizada pelo canal para armazenar itens em disco conforme necessário. No Windows, é usado % LOCALAPPDATA % ou % TEMP %, se nenhum outro caminho não for especificado explicitamente. Em ambientes que não sejam Windows, tem de especificar que uma localização válida ou telemetria não será armazenada no disco local.

## <a name="which-channel-should-i-use"></a>Que canal devo utilizar?

`ServerTelemetryChannel` Recomenda-se na maioria dos cenários de produção que envolvem os aplicativos de longa execução. O `Flush()` método implementado pelo `ServerTelemetryChannel` não é síncrona, e também não há garantia de envio de todos os itens pendentes de memória ou disco. Se utilizar este canal em cenários onde a aplicação está prestes a encerrar, é recomendável que introduzir algum atraso após chamar `Flush()`. A quantidade exata de atraso que poderá precisar não é previsível. Depende de fatores, como quantos itens ou `Transmission` instâncias estão na memória, quantas têm no disco, quantas estão a ser transmitidas para o back-end e se o canal está no meio de cenários de término exponenciais.

Se precisar de fazer um esvaziamento síncrono, recomendamos que utilize `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>O canal do Application Insights garante a entrega de telemetria? Se não o tiver feito, quais são os cenários em que telemetria pode ser perdida?

A resposta abreviada é que nenhum dos canais internos oferecem uma garantia de tipo de transação de entrega de telemetria para o back-end. `ServerTelemetryChannel` é mais avançada comparados com `InMemoryChannel` para uma entrega fiável, mas também faz apenas uma tentativa de melhor esforço para enviar telemetria. Telemetria ainda pode ser perdida em várias situações, incluindo esses cenários comuns:

1. Itens na memória serão perdidas quando a falha da aplicação.

1. A telemetria é perdida durante longos períodos de problemas de rede. Telemetria é armazenada no disco local durante falhas de rede ou quando ocorrem problemas com o Application Insights back-end. No entanto, os itens com mais de 24 horas são eliminadas.

1. As localizações de disco predefinido para armazenar a telemetria no Windows são % LOCALAPPDATA % ou % TEMP %. Estas localizações estão normalmente locais do computador. Se o aplicativo fisicamente migrada de um único local para outro, qualquer telemetria armazenada na localização original é perdida.

1. Nas aplicações Web no Windows, a localização de armazenamento em disco predefinido é D:\local\LocalAppData. Esta localização não é mantida. Ele serem eliminado em reinícios da aplicação, detalhes sobre o dimensionamento e outras essas operações, que leva a perda de telemetria armazenada nele. Pode substituir a predefinição e especificar armazenamento para uma localização persistente como D:\home. No entanto, tais locais persistentes são servidos pelo armazenamento remoto e por isso, podem ser lentas.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel funciona em sistemas que não seja o Windows?

Embora o nome do seu pacote e o espaço de nomes incluir "WindowsServer", este canal é suportado em sistemas diferentes do Windows, com a seguinte exceção. Em sistemas que não sejam Windows, o canal não cria uma pasta de armazenamento local por predefinição. Tem de criar uma pasta de armazenamento local e configurar o canal para utilizá-lo. Depois de configurar o armazenamento local, o canal funciona da mesma forma em todos os sistemas.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>O SDK cria um armazenamento temporário local? Os dados são encriptados no armazenamento?

O SDK armazena itens de telemetria no armazenamento local durante a problemas de rede ou durante a limitação. Estes dados não são criptografados localmente.

Para sistemas Windows, o SDK automaticamente cria uma pasta local temporária no diretório % LOCALAPPDATA % ou % TEMP % e restringe o acesso aos administradores e apenas o utilizador atual.

Para sistemas que não sejam Windows, sem armazenamento local é criado automaticamente pelo SDK e, portanto, nenhum dado é armazenado localmente por predefinição. Pode criar um diretório de armazenamento por conta própria e configurar o canal para utilizá-lo. Neste caso, é responsável por assegurar que o diretório está protegido.
Leia mais sobre [proteção de dados e privacidade](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK de código-fonte aberto
Como cada SDK do Application Insights, os canais são de código-fonte aberto. Ler e contribuir para o código, ou problemas de relatório, na [o repositório do GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Passos Seguintes

* [Amostragem](../../azure-monitor/app/sampling.md)
* [Resolução de problemas de SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
