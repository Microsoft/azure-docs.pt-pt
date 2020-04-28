---
title: Registo e diagnóstico
description: Explicação aprofundada de como gerar e recuperar a exploração madeireira e diagnósticos em Âncoras Espaciais Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74270136"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Exploração madeireira e diagnósticos em âncoras espaciais azure

A Azure Spatial Anchors fornece um mecanismo padrão de exploração de madeira que é útil para o desenvolvimento de aplicações. O modo de registo de diagnósticos de âncoras espaciais é útil quando necessita de mais informações para depuração. O registo de diagnósticos armazena imagens do ambiente.

## <a name="standard-logging"></a>Exploração madeireira padrão
Na API de Âncoras Espaciais, pode subscrever o mecanismo de exploração de madeira para obter registos úteis para o desenvolvimento de aplicações e depuração. As APIs de registo padrão não armazenam imagens do ambiente no disco do dispositivo. O SDK fornece estes registos como chamadas de eventos. Cabe-lhe a si integrar estes registos no mecanismo de exploração madeireira da aplicação.

### <a name="configuration-of-log-messages"></a>Configuração de mensagens de registo
Há duas chamadas de interesse para o utilizador. A amostra que se segue mostra como configurar a sessão.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Eventos e propriedades

Estas chamadas de eventos são fornecidas para processar registos e erros da sessão:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Especifica o nível de detalhe para os eventos a receber a partir do tempo de execução.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fornece eventos padrão de registo de depuração.
- [Erro](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Fornece eventos de registo que o tempo de execução considera erros.

## <a name="diagnostics-logging"></a>Registo de diagnósticos

Além do modo de funcionamento padrão para a exploração madeireira, as Âncoras Espaciais também têm um modo de diagnóstico. O modo de diagnóstico captura imagens do ambiente e regista-as no disco. Pode usar este modo para depurar certos tipos de problemas, como não localizar previsivelmente uma âncora. Ativar diagnósticos de login apenas para reproduzir um problema específico. Então desativa-o. Não ative diagnósticos quando estiver a executar as suas aplicações normalmente.

Durante uma interação de suporte com a Microsoft, um representante da Microsoft pode perguntar se está disposto a submeter um pacote de diagnóstico para uma investigação mais aprofundada. Neste caso, pode decidir ativar diagnósticos e reproduzir o problema para que possa submeter o pacote de diagnóstico.

Se submeter um registo de diagnóstico à Microsoft sem o reconhecimento prévio de um representante da Microsoft, a submissão ficará sem resposta.

As seguintes secções mostram como ativar o modo de diagnóstico e também como enviar registos de diagnóstico para a Microsoft.

### <a name="enable-diagnostics-logging"></a>Ativar o registo de diagnósticos

Quando ativa uma sessão de registo de diagnósticos, todas as operações da sessão têm registo de diagnósticos correspondentes no sistema de ficheiros local. Durante o registo, as imagens do ambiente são guardadas no disco.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Submeta o pacote de diagnóstico

O seguinte código de corte mostra como enviar um pacote de diagnóstico sintetizador para a Microsoft. Este pacote incluirá imagens do ambiente captadas pela sessão após permitir diagnósticos.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Partes de um pacote de diagnóstico
O pacote de diagnósticopode conter as seguintes informações:

- **Imagens de keyframe**: Imagens do ambiente captadas durante a sessão enquanto os diagnósticos foram ativados.
- **Registos**: Registar os eventos registados pelo tempo de execução.
- **Metadados**de sessão : Metadados que identificam a sessão.
