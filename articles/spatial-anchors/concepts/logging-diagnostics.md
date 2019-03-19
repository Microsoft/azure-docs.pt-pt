---
title: Registo e diagnóstico no âncoras espaciais do Azure | Documentos da Microsoft
description: Explicação detalhada sobre como gerar e obter o registo e diagnóstico no âncoras espaciais do Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882833"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registo e diagnóstico no âncoras espaciais do Azure

Âncoras espaciais do Azure fornece um mecanismo de log padrão que é útil para desenvolvimento de aplicações. O modo de registo de diagnóstico de âncoras espaciais é útil quando precisa de mais informações para depuração. Registo de diagnósticos armazena imagens do ambiente.

## <a name="standard-logging"></a>Log padrão
A API de âncoras espaciais, pode subscrever o mecanismo de log para obter registos úteis para o desenvolvimento de aplicativos e a depuração. As APIs de log padrão não armazene imagens do ambiente no disco do dispositivo. O SDK fornece estes registos como retornos de chamada do evento. Cabe-lhe integrar estes registos no mecanismo de log do aplicativo.

### <a name="configuration-of-log-messages"></a>Configuração de mensagens de registo
Existem dois retornos de chamada de interesse para o utilizador. O exemplo a seguir mostra como configurar a sessão.

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

Esses retornos de chamada de evento são fornecidos para processar registos e erros da sessão:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Especifica o nível de detalhe para os eventos receber do tempo de execução.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Fornece eventos de registo de depuração padrão.
- [Erro](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Fornece eventos de registo que o tempo de execução considera ser erros.

## <a name="diagnostics-logging"></a>Registo de diagnósticos

Além do modo padrão de operação para o registo, as âncoras espaciais também tem um modo de diagnóstico. Modo de diagnóstico captura de imagens do ambiente e regista-los no disco. Pode utilizar este modo para certos tipos de problemas, como falha de forma previsível localizar uma âncora de depuração. Ative diagnósticos de registo apenas para reproduzir um problema específico. Em seguida, desativá-la. Não ative o diagnóstico quando estiver a executar as suas aplicações normalmente.

Durante uma interação de suporte com a Microsoft, um representante da Microsoft pode perguntar se estiver disposto a enviar um pacote de diagnóstico para uma investigação mais aprofundada. Neste caso, pode optar por ativar os diagnósticos e reproduzir o problema para que pode enviar o pacote de diagnóstico. 

Se submeter um log de diagnóstico à Microsoft sem confirmação anterior de um representante da Microsoft, a submissão entram não respondida.

As secções seguintes mostram como ativar o modo de diagnóstico e também como submeter os registos de diagnóstico para a Microsoft.

### <a name="enable-diagnostics-logging"></a>Ativar o registo de diagnósticos

Quando ativa uma sessão para o log de diagnósticos, todas as operações na sessão tem diagnóstico correspondente, iniciar sessão no sistema de arquivos local. Durante o Registro em log, imagens de ambiente são guardadas no disco.

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

### <a name="submit-the-diagnostics-bundle"></a>Enviar o pacote de diagnóstico

O fragmento de código seguinte mostra como submeter um pacote de diagnóstico para a Microsoft. Este pacote inclui imagens do ambiente capturadas pela sessão depois de ativar o diagnóstico. 

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
O pacote de diagnóstico pode conter as seguintes informações:

- **Imagens de quadro-chave**: Imagens do ambiente capturado durante a sessão enquanto a diagnóstico foram ativado.
- **Registos**: Inicie a sessão de eventos registados pelo tempo de execução.
- **Metadados de sessão**: Metadados que identifica a sessão.
