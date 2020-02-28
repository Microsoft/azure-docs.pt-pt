---
title: Canais de telemetria em Insights de Aplicação Azure  Microsoft Docs
description: Como personalizar canais de telemetria em SDKs de Insights de Aplicação Azure para .NET e .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671125"
---
# <a name="telemetry-channels-in-application-insights"></a>Canais de telemetria em Insights de Aplicação

Os canais de telemetria são parte integrante dos [SDKs](../../azure-monitor/app/app-insights-overview.md)de Insights de Aplicação Azure . Gerem tampão e transmissão de telemetria para o serviço Application Insights. As versões .NET e .NET Core dos SDKs têm dois canais de telemetria incorporados: `InMemoryChannel` e `ServerTelemetryChannel`. Este artigo descreve cada canal em detalhe, incluindo como personalizar o comportamento do canal.

## <a name="what-are-telemetry-channels"></a>O que são canais de telemetria?

Os canais de telemetria são responsáveis por tamponar artigos de telemetria e enviá-los para o serviço Application Insights, onde são armazenados para consulta e análise. Um canal de telemetria é qualquer classe que implemente a interface [`Microsoft.ApplicationInsights.ITelemetryChannel`.](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

O método `Send(ITelemetry item)` de um canal de telemetria é chamado depois de todos os iniciais de telemetria e processadores de telemetria serem chamados. Então, quaisquer itens deixados por um processador de telemetria não chegarão ao canal. `Send()` normalmente não envia os artigos para a parte de trás instantaneamente. Tipicamente, amortece-os na memória e envia-os em lotes, para uma transmissão eficiente.

[Live Metrics Stream](live-stream.md) também tem um canal personalizado que alimenta o streaming ao vivo da telemetria. Este canal é independente do canal regular de telemetria, e este documento não se aplica a ele.

## <a name="built-in-telemetry-channels"></a>Canais de telemetria incorporados

Os Insights de Aplicação .NET e .NET Core SDKs com dois canais incorporados:

* `InMemoryChannel`: Um canal leve que tampona os itens na memória até serem enviados. Os itens são tamponados na memória e lavados uma vez a cada 30 segundos, ou sempre que 500 itens são tamponados. Este canal oferece garantias mínimas de fiabilidade porque não volta a tentar enviar telemetria após uma falha. Este canal também não mantém itens no disco, pelo que quaisquer itens não enviados são perdidos permanentemente após o encerramento da aplicação (gracioso ou não). Este canal implementa um método `Flush()` que pode ser usado para descarregar à força quaisquer itens de telemetria na memória sincronizadamente. Este canal é adequado para aplicações de curto prazo onde um flush sincronizado é ideal.

    Este canal faz parte do maior pacote Microsoft.ApplicationInsights NuGet e é o canal padrão que o SDK usa quando nada mais está configurado.

* `ServerTelemetryChannel`: Um canal mais avançado que tenha políticas de retry e a capacidade de armazenar dados num disco local. Este canal retenta o envio de telemetria se ocorrerem erros transitórios. Este canal também utiliza armazenamento de disco local para manter itens no disco durante interrupções de rede ou volumes elevados de telemetria. Devido a estes mecanismos de retry e armazenamento de discos locais, este canal é considerado mais fiável e é recomendado para todos os cenários de produção. Este canal é o padrão para [aplicações ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) que são configuradas de acordo com a documentação oficial. Este canal está otimizado para cenários de servidores com processos de longo prazo. O [método`Flush()`](#which-channel-should-i-use) implementado por este canal não é sincronizado.

    Este canal é enviado como o pacote Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet e é adquirido automaticamente quando utiliza o Microsoft.ApplicationInsights.Web ou Microsoft.ApplicationInsights.AspNetCore NuGet pacote.

## <a name="configure-a-telemetry-channel"></a>Configure um canal de telemetria

Configura um canal de telemetria definindo-o na configuração ativa da telemetria. Para aplicações ASP.NET, a configuração envolve definir a instância do canal de telemetria para `TelemetryConfiguration.Active`, ou modificando `ApplicationInsights.config`. Para ASP.NET aplicações Core, a configuração envolve adicionar o canal ao recipiente de injeção de dependência.

As seguintes secções mostram exemplos de configuração da configuração `StorageFolder` para o canal em vários tipos de aplicações. `StorageFolder` é apenas uma das configurações configuráveis. Para obter a lista completa de definições de configuração, consulte a secção de [definições](telemetry-channels.md#configurable-settings-in-channels) mais tarde neste artigo.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Configuração utilizando ApplicationInsights.config para aplicações ASP.NET

A seguinte secção do [ApplicationInsights.config](configuration-with-applicationinsights-config.md) mostra o canal `ServerTelemetryChannel` configurado com `StorageFolder` definido para uma localização personalizada:

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

O código seguinte configura uma instância 'ServerTelemettryChannel' com `StorageFolder` definida para uma localização personalizada. Adicione este código no início da aplicação, tipicamente em `Application_Start()` método em Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Configuração em código para aplicações ASP.NET Core

Modificar o método `ConfigureServices` da classe `Startup.cs`, como mostrado aqui:

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
> Configurar o canal utilizando `TelemetryConfiguration.Active` não é recomendado para ASP.NET aplicações Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Configuração em código para aplicações de consola .NET/.NET Core

Para aplicações de consola, o código é o mesmo para .NET e .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Detalhes operacionais do ServerTelemettryChannel

`ServerTelemetryChannel` lojas que chegam a itens num amortecedor de memória. Os artigos são serializados, comprimidos e armazenados numa `Transmission` instância uma vez a cada 30 segundos, ou quando 500 itens foram tamponados. Uma única `Transmission` instância contém até 500 itens e representa um lote de telemetria que é enviado por uma única chamada HTTPS para o serviço Application Insights.

Por predefinição, um máximo de 10 `Transmission` instâncias podem ser enviadas em paralelo. Se a telemetria chegar a tarifas mais rápidas, ou se a rede ou a extremidade traseira do Application Insights for lenta, `Transmission` casos são armazenados na memória. A capacidade padrão deste tampão de `Transmission` em memória é de 5 MB. Quando a capacidade de memória é ultrapassada, `Transmission` instâncias são armazenadas em disco local até um limite de 50 MB. `Transmission` casos são armazenados no disco local também quando há problemas de rede. Apenas os itens que estão guardados num disco local sobrevivem a uma falha de aplicação. São enviados sempre que a candidatura recomeça.

## <a name="configurable-settings-in-channels"></a>Configurações configuráveis em canais

Para obter a lista completa de configurações configuráveis para cada canal, consulte:

* [Canal inMemory](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [Canal de Telemetria server](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Aqui estão as definições mais usadas para `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: A quantidade máxima de memória, em bytes, utilizada pelo canal para as transmissões tampão na memória. Quando esta capacidade é alcançada, novos itens são armazenados diretamente no disco local. O valor padrão é de 5 MB. A definição de um valor mais elevado leva a uma utilização menos útil do disco, mas lembre-se que os itens na memória serão perdidos se a aplicação falhar.

1. `MaxTransmissionSenderCapacity`: O número máximo de `Transmission` casos que serão enviados ao Mesmo tempo para Informações de Aplicação. O valor padrão é 10. Esta definição pode ser configurada para um número mais elevado, o que é recomendado quando um grande volume de telemetria é gerado. O volume elevado ocorre normalmente durante os testes de carga ou quando a amostragem é desligada.

1. `StorageFolder`: A pasta utilizada pelo canal para armazenar itens no disco conforme necessário. No Windows, quer %LOCALAPPDATA% quer %TEMP% é utilizado se nenhum outro caminho for especificado explicitamente. Em ambientes que não o Windows, deve especificar que uma localização válida ou telemetria não serão armazenadas no disco local.

## <a name="which-channel-should-i-use"></a>Que canal devo usar?

`ServerTelemetryChannel` é recomendado para a maioria dos cenários de produção envolvendo aplicações de longo prazo. O método `Flush()` implementado pela `ServerTelemetryChannel` não é sincronizado, e também não garante o envio de todos os itens pendentes da memória ou do disco. Se utilizar este canal em cenários em que a aplicação está prestes a ser encerrada, recomendamos que introduza algum atraso depois de ligar para `Flush()`. A quantidade exata de atraso que pode exigir não é previsível. Depende de fatores como quantos itens ou `Transmission` casos estão na memória, quantos estão no disco, quantos estão a ser transmitidos para a parte de trás, e se o canal está no meio de cenários de back-off exponenciais.

Se precisar de fazer um flush sincronizado, recomendamos que utilize `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>O canal Application Insights garante a entrega de telemetria? Se não, quais são os cenários em que a telemetria pode ser perdida?

A resposta curta é que nenhum dos canais incorporados oferece uma garantia de entrega de telemetria para o final. `ServerTelemetryChannel` é mais avançado em comparação com `InMemoryChannel` para uma entrega fiável, mas também faz apenas uma tentativa de melhor esforço para enviar telemetria. A telemetria ainda pode perder-se em várias situações, incluindo estes cenários comuns:

1. Os itens na memória perdem-se quando a aplicação se despenha.

1. A telemetria perde-se durante longos períodos de problemas de rede. A telemetria é armazenada no disco local durante as interrupções de rede ou quando ocorrem problemas com a extremidade de fundo da Aplicação Insights. No entanto, artigos com mais de 48 horas são descartados.

1. As localizações padrão do disco para armazenar telemetria no Windows são %LOCALAPPDATA% ou %TEMP%. Estes locais são tipicamente locais para a máquina. Se a aplicação migrar fisicamente de um local para outro, perde-se qualquer telemetria armazenada no local original.

1. Nas Aplicações Web do Windows, a localização padrão de armazenamento de discos é D:\local\LocalAppData. Este local não é persistiu. É eliminado em reinícios de aplicações, escalas e outras operações deste tipo, levando à perda de qualquer telemetria armazenada lá. Pode anular o padrão e especificar o armazenamento para um local persificada como D:\home. No entanto, tais locais persistidos são servidos por armazenamento remoto e assim podem ser lentos.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>O ServerTelemettryChannel funciona em outros sistemas que não o Windows?

Embora o nome do seu pacote e espaço de nome inclua "WindowsServer", este canal é suportado em outros sistemas que não o Windows, com a seguinte exceção. Em sistemas que não o Windows, o canal não cria uma pasta de armazenamento local por padrão. É necessário criar uma pasta de armazenamento local e configurar o canal para a utilizar. Depois de configurado o armazenamento local, o canal funciona da mesma forma em todos os sistemas.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>O SDK cria armazenamento local temporário? Os dados estão encriptados no armazenamento?

O SDK armazena artigos de telemetria no armazenamento local durante problemas de rede ou durante o estrangulamento. Estes dados não são encriptados localmente.

Para os sistemas Windows, o SDK cria automaticamente uma pasta local temporária no diretório %TEMP% ou %LOCALAPPDATA% e restringe o acesso a administradores e apenas ao utilizador atual.

Para sistemas que não o Windows, nenhum armazenamento local é criado automaticamente pelo SDK, pelo que nenhum dado é armazenado localmente por padrão. Você pode criar um diretório de armazenamento e configurar o canal para usá-lo. Neste caso, é responsável por garantir que o diretório está seguro.
Leia mais sobre proteção de [dados e privacidade.](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)

## <a name="open-source-sdk"></a>SDK de código aberto
Como todos os SDK para Insights de Aplicação, os canais são de código aberto. Leia e contribua para o código, ou reporte problemas, [no repo oficial do GitHub.](https://github.com/Microsoft/ApplicationInsights-dotnet)

## <a name="next-steps"></a>Passos seguintes

* [Amostragem](../../azure-monitor/app/sampling.md)
* [Resolução de problemas da SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
