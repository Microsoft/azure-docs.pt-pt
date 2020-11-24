---
title: Registo e diagnóstico
description: Explicação aprofundada de como gerar e recuperar o registo e diagnósticos em Âncoras Espaciais Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494646"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registo e diagnósticos em Âncoras Espaciais Azure

A Azure Spatial Anchors fornece um mecanismo padrão de registo que é útil para o desenvolvimento de aplicações. O modo de registo de diagnósticos de âncoras espaciais é útil quando se precisa de mais informações para depurar. O registo de diagnósticos armazena imagens do ambiente.

## <a name="standard-logging"></a>Registo padrão
Na API de Âncoras Espaciais, pode subscrever o mecanismo de registo para obter registos úteis para o desenvolvimento de aplicações e depuração. As APIs de registo padrão não armazenam imagens do ambiente no disco do dispositivo. O SDK fornece estes registos como chamadas de eventos. Cabe-lhe a si integrar estes registos no mecanismo de registo da aplicação.

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

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Especifica o nível de detalhe para os eventos a receber a partir do tempo de funcionamento.
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fornece eventos padrão de registo de depurg.
- [Erro](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Fornece eventos de registo que o tempo de execução considera serem erros.

## <a name="diagnostics-logging"></a>Registo de diagnósticos

Além do modo padrão de funcionamento para a exploração madeireira, a Spatial Anchors também tem um modo de diagnóstico. O modo de diagnóstico captura imagens do ambiente e regista-as no disco. Pode utilizar este modo para depurar certos tipos de problemas, como não localizar previsivelmente uma âncora. Permitir o registo de diagnósticos apenas para reproduzir um problema específico. Então desative-o. Não ative diagnósticos quando estiver a executar as suas aplicações normalmente.

Durante uma interação de suporte com a Microsoft, um representante da Microsoft pode perguntar se está disposto a submeter um pacote de diagnósticos para uma investigação mais aprofundada. Neste caso, poderá decidir ativar diagnósticos e reproduzir o problema para que possa submeter o pacote de diagnóstico.

Se submeter um registo de diagnóstico à Microsoft sem o aviso prévio de um representante da Microsoft, a submissão ficará sem resposta.

As secções seguintes mostram como ativar o modo de diagnóstico e também como enviar registos de diagnóstico para a Microsoft.

### <a name="enable-diagnostics-logging"></a>Ativar o registo de diagnósticos

Quando ativa uma sessão para o registo de diagnósticos, todas as operações na sessão têm o correspondente registo de diagnósticos no sistema de ficheiros local. Durante a exploração madeireira, as imagens do ambiente são guardadas no disco.

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

### <a name="submit-the-diagnostics-bundle"></a>Submeta o pacote de diagnósticos

O seguinte código snippet mostra como enviar um pacote de diagnósticos para a Microsoft. Este pacote incluirá imagens do ambiente capturado pela sessão depois de ativar diagnósticos.

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

### <a name="parts-of-a-diagnostics-bundle"></a>Partes de um pacote de diagnósticos
O pacote de diagnósticos pode conter as seguintes informações:

- **Imagens do quadro-chave**: Imagens do ambiente captadas durante a sessão enquanto os diagnósticos estavam ativados.
- **Registos**: Registar eventos registados pelo tempo de execução.
- **Metadados de sessão**: Metadados que identificam a sessão.