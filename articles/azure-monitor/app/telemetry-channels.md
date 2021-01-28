---
title: Canais de telemetria em Azure Application Insights | Microsoft Docs
description: Como personalizar canais de telemetria em Azure Application Insights SDKs para .NET e .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: a22a0d112671019d73eb4c9a3853462e4e9c8c75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937358"
---
# <a name="telemetry-channels-in-application-insights"></a>Canais de telemetria em Insights de Aplicação

Os canais de telemetria são parte integrante dos [SDKs Azure Application Insights](./app-insights-overview.md). Gerem a buffering e a transmissão da telemetria para o serviço Application Insights. As versões .NET e .NET Core dos SDKs têm dois canais de telemetria incorporados: `InMemoryChannel` e `ServerTelemetryChannel` . Este artigo descreve cada canal em detalhe, incluindo como personalizar o comportamento do canal.

## <a name="what-are-telemetry-channels"></a>O que são canais de telemetria?

Os canais de telemetria são responsáveis pela tamponagem de itens de telemetria e pelo envio para o serviço Application Insights, onde estão armazenados para consulta e análise. Um canal de telemetria é qualquer classe que implemente a [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel) interface.

O `Send(ITelemetry item)` método de um canal de telemetria é chamado depois de todos os inicializadores de telemetria e processadores de telemetria serem chamados. Então, quaisquer itens deixados por um processador de telemetria não chegam ao canal. `Send()` não costuma enviar os itens para a parte de trás instantaneamente. Tipicamente, tampona-os na memória e envia-os em lotes, para uma transmissão eficiente.

[Live Metrics Stream](live-stream.md) também tem um canal personalizado que alimenta o streaming ao vivo da telemetria. Este canal é independente do canal regular de telemetria, e este documento não se aplica a ele.

## <a name="built-in-telemetry-channels"></a>Canais de telemetria incorporados

O navio Application Insights .NET e .NET Core SDKs com dois canais incorporados:

* `InMemoryChannel`: Um canal leve que tampona os itens na memória até serem enviados. Os itens são tamponados na memória e lavados uma vez a cada 30 segundos, ou sempre que 500 itens são tamponados. Este canal oferece garantias mínimas de fiabilidade porque não recandiduz o envio de telemetria após uma falha. Este canal também não guarda itens no disco, pelo que quaisquer itens não solicitados são perdidos permanentemente após o encerramento da aplicação (gracioso ou não). Este canal implementa um `Flush()` método que pode ser usado para descarregar qualquer itens de telemetria na memória sincronizados. Este canal é adequado para aplicações de curto prazo onde um flush sincronizado é ideal.

    Este canal faz parte do pacote NuGet microsoft.ApplicationInsights maior e é o canal padrão que o SDK utiliza quando nada mais está configurado.

* `ServerTelemetryChannel`: Um canal mais avançado que tenha políticas de retenção e capacidade de armazenar dados num disco local. Este canal recauchutado envia telemetria se ocorrerem erros transitórios. Este canal também utiliza o armazenamento de discos locais para manter os itens no disco durante interrupções de rede ou volumes elevados de telemetria. Devido a estes mecanismos de retenção e armazenamento de discos locais, este canal é considerado mais fiável e é recomendado para todos os cenários de produção. Este canal é o padrão para [aplicações ASP.NET](./asp-net.md) e [ASP.NET Core](./asp-net-core.md) que são configuradas de acordo com a documentação oficial. Este canal está otimizado para cenários de servidor com processos de longa duração. O [`Flush()`](#which-channel-should-i-use) método implementado por este canal não é sincronizado.

    Este canal é enviado como o pacote Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet e é adquirido automaticamente quando utiliza o pacote Microsoft.ApplicationInsights.Web ou Microsoft.ApplicationInsights.AspNetCore NuGet.

## <a name="configure-a-telemetry-channel"></a>Configurar um canal de telemetria

Configura um canal de telemetria definindo-o para a configuração de telemetria ativa. Para ASP.NET aplicações, a configuração envolve definir a instância do canal de telemetria para `TelemetryConfiguration.Active` , ou modificando `ApplicationInsights.config` . Para aplicações ASP.NET Core, a configuração envolve a adição do canal ao Recipiente de Injeção de Dependência.

As secções seguintes mostram exemplos de configuração da `StorageFolder` definição do canal em vários tipos de aplicações. `StorageFolder` é apenas uma das configurações configuráveis. Para obter a lista completa das definições de configuração, consulte [a secção de definições](#configurable-settings-in-channels) mais tarde neste artigo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração utilizando ApplicationInsights.config para aplicações ASP.NET

A seguinte secção de [ApplicationInsights.config](configuration-with-applicationinsights-config.md) mostra o `ServerTelemetryChannel` canal configurado com `StorageFolder` configuração para uma localização personalizada:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>Configuração em código para aplicações ASP.NET

O código seguinte configura uma instância 'ServerTelemetryChannel' com `StorageFolder` definido para uma localização personalizada. Adicione este código no início da aplicação, normalmente em `Application_Start()` método em Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuração em código para aplicações core ASP.NET

Modifique o `ConfigureServices` método da classe como mostrado `Startup.cs` aqui:

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
> A configuração do canal utilizando `TelemetryConfiguration.Active` não é recomendada para aplicações ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuração em código para aplicações de consola .NET/.NET Core

Para aplicações de consola, o código é o mesmo tanto para .NET como para .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais do ServerTelemetryChannel

`ServerTelemetryChannel` lojas que chegam itens em um tampão de memória. Os itens são serializados, comprimidos e armazenados numa `Transmission` instância uma vez a cada 30 segundos, ou quando 500 itens foram tamponados. Uma única `Transmission` instância contém até 500 itens e representa um lote de telemetria que é enviado através de uma única chamada HTTPS para o serviço Application Insights.

Por predefinição, um máximo de 10 `Transmission` instâncias podem ser enviadas em paralelo. Se a telemetria estiver a chegar a taxas mais rápidas, ou se a rede ou a parte de trás do Application Insights for lenta, `Transmission` as instâncias são armazenadas na memória. A capacidade predefinida deste tampão na memória é de `Transmission` 5 MB. Quando a capacidade na memória foi excedida, `Transmission` as instâncias são armazenadas no disco local até um limite de 50 MB. `Transmission` as instâncias são armazenadas no disco local também quando há problemas de rede. Apenas os itens que estão armazenados num disco local sobrevivem a uma falha de aplicação. São enviados sempre que o pedido recomeça.

## <a name="configurable-settings-in-channels"></a>Configurações configuráveis nos canais

Para obter a lista completa de configurações configuráveis para cada canal, consulte:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Aqui estão as definições mais usadas `ServerTelemetryChannel` para:

1. `MaxTransmissionBufferCapacity`: A quantidade máxima de memória, em bytes, utilizada pelo canal para as transmissões tampão na memória. Quando esta capacidade é alcançada, novos itens são armazenados diretamente no disco local. O valor predefinido é de 5 MB. Definir um valor mais elevado leva a uma menor utilização do disco, mas lembre-se que os itens na memória serão perdidos se a aplicação falhar.

1. `MaxTransmissionSenderCapacity`: O número máximo de `Transmission` ocorrências que serão enviadas ao Mesmo tempo para a Application Insights. O valor predefinido é 10. Esta definição pode ser configurada para um número mais elevado, o que é recomendado quando um grande volume de telemetria é gerado. O volume elevado ocorre normalmente durante os testes de carga ou quando a amostragem é desligada.

1. `StorageFolder`: A pasta utilizada pelo canal para armazenar itens em disco, se necessário. No Windows, ou %LOCALAPPDATA% ou %TEMP% é utilizado se nenhuma outra via for especificada explicitamente. Em ambientes diferentes do Windows, deve especificar que uma localização válida ou telemetria não será armazenada no disco local.

## <a name="which-channel-should-i-use"></a>Que canal devo usar?

`ServerTelemetryChannel` é recomendado para a maioria dos cenários de produção envolvendo aplicações de longa duração. O `Flush()` método implementado por não é `ServerTelemetryChannel` sincronizado, e também não garante o envio de todos os itens pendentes da memória ou do disco. Se utilizar este canal em cenários onde a aplicação está prestes a ser encerrada, recomendamos que introduza algum atraso após a chamada `Flush()` . A quantidade exata de atraso que podes precisar não é previsível. Depende de fatores como quantos itens ou `Transmission` instâncias estão na memória, quantos estão no disco, quantos estão a ser transmitidos para a parte de trás, e se o canal está no meio de cenários exponenciais de back-off.

Se precisar de fazer um autoclismo sincronizado, recomendamos que utilize `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>O canal Application Insights garante a entrega de telemetria? Caso contrário, quais são os cenários em que a telemetria pode ser perdida?

A resposta curta é que nenhum dos canais incorporados oferece uma garantia de entrega de telemetria tipo de transação na parte de trás. `ServerTelemetryChannel` é mais avançado em comparação com `InMemoryChannel` para entrega fiável, mas também faz apenas uma tentativa de melhor esforço para enviar telemetria. A telemetria ainda pode ser perdida em várias situações, incluindo estes cenários comuns:

1. Os itens na memória perdem-se quando a aplicação falha.

1. A telemetria perde-se durante longos períodos de problemas de rede. A telemetria é armazenada no disco local durante as interrupções da rede ou quando ocorrem problemas com a parte de trás do Application Insights. No entanto, itens com mais de 48 horas são descartados.

1. As localizações de disco predefinida para armazenar telemetria no Windows são %LOCALAPPDATA% ou %TEMP%. Estes locais são normalmente locais para a máquina. Se a aplicação migrar fisicamente de um local para outro, qualquer telemetria armazenada no local original é perdida.

1. Nas aplicações web no Windows, a localização padrão de armazenamento de disco é D:\local\LocalAppData. Este local não persiste. É eliminado em reinicialização de apps, escalas e outras operações deste tipo, levando à perda de qualquer telemetria armazenada lá. Pode anular o predefinido e especificar o armazenamento para um local persistido como D:\home. No entanto, tais locais persistidos são servidos por armazenamento remoto e assim podem ser lentos.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>O ServerTelemetryChannel funciona em sistemas que não o Windows?

Embora o nome do seu pacote e espaço de nome inclua "WindowsServer", este canal é suportado em sistemas que não o Windows, com a seguinte exceção. Em sistemas que não o Windows, o canal não cria uma pasta de armazenamento local por padrão. Tem de criar uma pasta de armazenamento local e configurar o canal para a utilizar. Depois de configurado o armazenamento local, o canal funciona da mesma forma em todos os sistemas.

> [!NOTE]
> Com o lançamento 2.15.0-beta3 e maior armazenamento local é agora automaticamente criado para Linux, Mac e Windows. Para sistemas não Windows, o SDK criará automaticamente uma pasta de armazenamento local com base na seguinte lógica:
> - `${TMPDIR}` - se `${TMPDIR}` a variável ambiental for definida, esta localização é utilizada.
> - `/var/tmp` - se a localização anterior não existir, `/var/tmp` tentamos.
> - `/tmp` - se ambas as localizações anteriores não existirem, `tmp` tentamos. 
> - Se nenhum desses locais existir armazenamento local não é criado e a configuração manual ainda é necessária. [Para mais detalhes de implementação.](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860)

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>O SDK cria armazenamento local temporário? Os dados estão encriptados no armazenamento?

O SDK armazena artigos de telemetria no armazenamento local durante problemas de rede ou durante o estrangulamento. Estes dados não são encriptados localmente.

Para os sistemas Windows, o SDK cria automaticamente uma pasta local temporária no diretório %TEMP% ou %LOCALAPPDATA% e limita o acesso apenas aos administradores e ao utilizador atual.

Para sistemas que não o Windows, nenhum armazenamento local é criado automaticamente pelo SDK, pelo que nenhum dado é armazenado localmente por padrão.

> [!NOTE]
> Com o lançamento 2.15.0-beta3 e maior armazenamento local é agora automaticamente criado para Linux, Mac e Windows. 

 Você mesmo pode criar um diretório de armazenamento e configurar o canal para usá-lo. Neste caso, é responsável por garantir que o diretório está seguro.
Leia mais sobre [proteção de dados e privacidade.](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)

## <a name="open-source-sdk"></a>SDK de código aberto
Como todos os SDK para Insights de Aplicações, os canais são de código aberto. Leia e contribua para o código, ou reporte problemas, [no repo oficial do GitHub.](https://github.com/Microsoft/ApplicationInsights-dotnet)

## <a name="next-steps"></a>Próximos passos

* [Amostragem](./sampling.md)
* [Resolução de problemas da SDK](./asp-net-troubleshoot-no-data.md)

