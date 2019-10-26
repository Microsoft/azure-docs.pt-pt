---
title: Canais de telemetria no Aplicativo Azure insights | Microsoft Docs
description: Como personalizar canais de telemetria nos SDKs do Aplicativo Azure insights para .NET e .NET Core.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cb7b9047e1036a2ab4bfd94cca88589dcdcd0ca3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899556"
---
# <a name="telemetry-channels-in-application-insights"></a>Canais de telemetria no Application Insights

Os canais de telemetria são parte integrante dos [SDKs do aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md). Eles gerenciam o armazenamento em buffer e a transmissão de telemetria para o serviço de Application Insights. As versões .NET e .NET Core dos SDKs têm dois canais internos de telemetria: `InMemoryChannel` e `ServerTelemetryChannel`. Este artigo descreve cada canal em detalhes, incluindo como personalizar o comportamento do canal.

## <a name="what-are-telemetry-channels"></a>O que são canais de telemetria?

Os canais de telemetria são responsáveis por armazenar em buffer itens de telemetria e enviá-los para o serviço de Application Insights, onde são armazenados para consulta e análise. Um canal de telemetria é qualquer classe que implementa a interface [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) .

O método `Send(ITelemetry item)` de um canal de telemetria é chamado depois que todos os inicializadores de telemetria e processadores de telemetria são chamados. Assim, todos os itens descartados por um processador de telemetria não alcançarão o canal. o `Send()` normalmente não envia os itens para o back-end instantaneamente. Normalmente, ele os armazena em buffers na memória e os envia em lotes para transmissão eficiente.

[Live Metrics Stream](live-stream.md) também tem um canal personalizado que alimenta a transmissão ao vivo da telemetria. Esse canal é independente do canal de telemetria regular e este documento não se aplica a ele.

## <a name="built-in-telemetry-channels"></a>Canais internos de telemetria

Os SDKs Application Insights .NET e .NET Core são fornecidos com dois canais internos:

* `InMemoryChannel`: um canal leve que armazena em buffer os itens na memória até que eles sejam enviados. Os itens são armazenados em buffer na memória e liberados uma vez a cada 30 segundos, ou sempre que 500 itens são armazenados em buffer. Esse canal oferece garantias de confiabilidade mínimas porque não tenta enviar novamente a telemetria após uma falha. Esse canal também não mantém itens em disco, portanto, todos os itens não enviados são perdidos permanentemente após o desligamento do aplicativo (normal ou não). Esse canal implementa um método `Flush()` que pode ser usado para forçar a liberação de todos os itens de telemetria na memória de forma síncrona. Esse canal é adequado para aplicativos de curta execução em que uma liberação síncrona é ideal.

    Esse canal faz parte do pacote NuGet Microsoft. ApplicationInsights maior e é o canal padrão que o SDK usa quando nada mais está configurado.

* `ServerTelemetryChannel`: um canal mais avançado que tem políticas de repetição e a capacidade de armazenar dados em um disco local. Esse canal tentará enviar telemetria novamente se ocorrerem erros transitórios. Esse canal também usa o armazenamento em disco local para manter os itens em disco durante interrupções de rede ou grandes volumes de telemetria. Devido a esses mecanismos de repetição e armazenamento de disco local, esse canal é considerado mais confiável e é recomendado para todos os cenários de produção. Esse canal é o padrão para aplicativos [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) configurados de acordo com a documentação oficial. Esse canal é otimizado para cenários de servidor com processos de execução longa. O método de [`Flush()`](#which-channel-should-i-use) que é implementado por esse canal não é síncrono.

    Esse canal é fornecido como o pacote NuGet Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel e é adquirido automaticamente quando você usa o NuGet Microsoft. ApplicationInsights. Web ou Microsoft. ApplicationInsights. AspNetCore agrupa.

## <a name="configure-a-telemetry-channel"></a>Configurar um canal de telemetria

Configure um canal de telemetria definindo-o como a configuração de telemetria ativa. Para aplicativos ASP.NET, a configuração envolve definir a instância de canal de telemetria para `TelemetryConfiguration.Active`ou modificando `ApplicationInsights.config`. Para aplicativos ASP.NET Core, a configuração envolve adicionar o canal ao contêiner de injeção de dependência.

As seções a seguir mostram exemplos de como definir a configuração de `StorageFolder` para o canal em vários tipos de aplicativos. `StorageFolder` é apenas uma das configurações configuráveis. Para obter a lista completa de definições de configuração, consulte [a seção Configurações](telemetry-channels.md#configurable-settings-in-channels) mais adiante neste artigo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração usando ApplicationInsights. config para aplicativos ASP.NET

A seção a seguir de [ApplicationInsights. config](configuration-with-applicationinsights-config.md) mostra o canal de `ServerTelemetryChannel` configurado com `StorageFolder` definido como um local personalizado:

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

O código a seguir configura uma instância de ' ServerTelemetryChannel ' com `StorageFolder` definido como um local personalizado. Adicione esse código no início do aplicativo, normalmente no método `Application_Start()` no Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuração no código para aplicativos ASP.NET Core

Modifique o método `ConfigureServices` da classe `Startup.cs`, conforme mostrado aqui:

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
> A configuração do canal usando `TelemetryConfiguration.Active` não é recomendada para aplicativos ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuração no código para aplicativos de console .NET/.NET Core

Para aplicativos de console, o código é o mesmo para .NET e .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais do ServerTelemetryChannel

`ServerTelemetryChannel` armazena itens de chegada em um buffer na memória. Os itens são serializados, compactados e armazenados em uma instância de `Transmission` uma vez a cada 30 segundos ou quando 500 itens tiverem sido armazenados em buffer. Uma única instância de `Transmission` contém até 500 itens e representa um lote de telemetria enviado por uma única chamada HTTPS para o serviço Application Insights.

Por padrão, um máximo de 10 instâncias de `Transmission` pode ser enviado em paralelo. Se a telemetria estiver chegando a taxas mais rápidas ou se a rede ou o back-end de Application Insights estiver lento, `Transmission` instâncias serão armazenadas na memória. A capacidade padrão desse buffer de `Transmission` na memória é de 5 MB. Quando a capacidade na memória for excedida, `Transmission` instâncias serão armazenadas no disco local até um limite de 50 MB. `Transmission` instâncias são armazenadas no disco local também quando há problemas de rede. Somente os itens que são armazenados em um disco local sobrevivem a uma falha do aplicativo. Eles são enviados sempre que o aplicativo é iniciado novamente.

## <a name="configurable-settings-in-channels"></a>Configurações configuráveis em canais

Para obter a lista completa de configurações configuráveis para cada canal, consulte:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Aqui estão as configurações mais comumente usadas para `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: a quantidade máxima de memória, em bytes, usada pelo canal para transmissões de buffer na memória. Quando essa capacidade é atingida, novos itens são armazenados diretamente no disco local. O valor padrão é 5 MB. Definir um valor mais alto leva a menos uso do disco, mas lembre-se de que os itens na memória serão perdidos se o aplicativo falhar.

1. `MaxTransmissionSenderCapacity`: o número máximo de instâncias de `Transmission` que serão enviadas para Application Insights ao mesmo tempo. O valor padrão é 10. Essa configuração pode ser configurada para um número mais alto, que é recomendado quando um grande volume de telemetria é gerado. O alto volume geralmente ocorre durante o teste de carga ou quando a amostragem está desativada.

1. `StorageFolder`: a pasta usada pelo canal para armazenar itens no disco, conforme necessário. No Windows, o% LOCALAPPDATA% ou% TEMP% será usado se nenhum outro caminho for especificado explicitamente. Em ambientes diferentes do Windows, você deve especificar um local válido ou a telemetria não será armazenada no disco local.

## <a name="which-channel-should-i-use"></a>Qual canal devo usar?

`ServerTelemetryChannel` é recomendada para a maioria dos cenários de produção que envolvem aplicativos de execução longa. O método `Flush()` implementado pelo `ServerTelemetryChannel` não é síncrono e também não garante o envio de todos os itens pendentes da memória ou do disco. Se você usar esse canal em cenários em que o aplicativo está prestes a ser desligado, recomendamos que você introduza algum atraso depois de chamar `Flush()`. A quantidade exata de atraso que você pode exigir não é previsível. Depende de fatores como quantos itens ou instâncias de `Transmission` estão na memória, quantas estão no disco, quantos estão sendo transmitidos para o back-end e se o canal está no meio de cenários de retirada exponencial.

Se você precisar fazer uma liberação síncrona, recomendamos o uso de `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>O canal Application Insights garante a entrega de telemetria? Caso contrário, quais são os cenários em que a telemetria pode ser perdida?

A resposta curta é que nenhum dos canais internos oferece uma garantia de tipo de transação de entrega de telemetria para o back-end. a `ServerTelemetryChannel` é mais avançada em comparação com `InMemoryChannel` para entrega confiável, mas também faz apenas uma tentativa de envio de telemetria por um melhor esforço. A telemetria ainda pode ser perdida em várias situações, incluindo estes cenários comuns:

1. Os itens na memória são perdidos quando o aplicativo falha.

1. A telemetria é perdida durante períodos estendidos de problemas de rede. A telemetria é armazenada no disco local durante interrupções de rede ou quando ocorrem problemas com o back-end de Application Insights. No entanto, os itens com mais de 24 horas são descartados.

1. Os locais de disco padrão para armazenar telemetria no Windows são% LOCALAPPDATA% ou% TEMP%. Esses locais normalmente são locais para o computador. Se o aplicativo migrar fisicamente de um local para outro, qualquer telemetria armazenada no local original será perdida.

1. Em aplicativos Web no Windows, o local de armazenamento de disco padrão é D:\local\LocalAppData. Esse local não é persistente. Ele é apagado em reinicializações de aplicativo, escalações e outras operações, levando à perda de qualquer telemetria armazenada lá. Você pode substituir o padrão e especificar o armazenamento para um local persistente, como D:\home. No entanto, esses locais persistentes são atendidos pelo armazenamento remoto e, portanto, podem ser lentos.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>O ServerTelemetryChannel funciona em sistemas diferentes do Windows?

Embora o nome de seu pacote e namespace inclua "WindowsServer", esse canal tem suporte em sistemas diferentes do Windows, com a seguinte exceção. Em sistemas diferentes do Windows, o canal não cria uma pasta de armazenamento local por padrão. Você deve criar uma pasta de armazenamento local e configurar o canal para usá-la. Depois que o armazenamento local tiver sido configurado, o canal funcionará da mesma maneira em todos os sistemas.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>O SDK cria um armazenamento local temporário? Os dados são criptografados no armazenamento?

O SDK armazena itens de telemetria no armazenamento local durante problemas de rede ou durante a limitação. Esses dados não são criptografados localmente.

Para sistemas Windows, o SDK cria automaticamente uma pasta local temporária no diretório% TEMP% ou% LOCALAPPDATA% e restringe o acesso a administradores e somente ao usuário atual.

Para sistemas diferentes do Windows, nenhum armazenamento local é criado automaticamente pelo SDK e, portanto, nenhum dado é armazenado localmente por padrão. Você pode criar um diretório de armazenamento por conta própria e configurar o canal para usá-lo. Nesse caso, você é responsável por garantir que o diretório esteja protegido.
Leia mais sobre [proteção e privacidade de dados](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>SDK de código-fonte aberto
Assim como todos os SDK para Application Insights, os canais são de software livre. Leia e contribua com o código ou relate problemas no [repositório GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Passos seguintes

* [Amostragem](../../azure-monitor/app/sampling.md)
* [Solução de problemas do SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
