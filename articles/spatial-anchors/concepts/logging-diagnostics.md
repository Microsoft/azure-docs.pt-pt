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
ms.openlocfilehash: d9377e2b5b66a7d426373a8a85e4880dafeaeee6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753289"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Registo e diagnóstico no âncoras espaciais do Azure

Âncoras espaciais do Azure fornece um mecanismo de log padrão útil para desenvolvimento de aplicações. Além disso, existe um modo de registo de diagnóstico útil quando são necessárias para depuração ainda mais informações. Registo de diagnósticos inclui o armazenamento de imagens do ambiente.

## <a name="standard-logging-in-azure-spatial-anchors"></a>Início de sessão padrão âncoras espaciais do Azure
A API de âncoras espaciais do Azure fornece um mecanismo de log que aplicações podem subscrever para receber registos úteis no desenvolvimento de aplicativos e a depuração. As APIs de log padrão não manter imagens de ambiente para o disco do dispositivo. O SDK fornece estes registos como retornos de chamada do evento. Cabe-lhe integrar estes registos no mecanismo de log do aplicativo.

### <a name="how-to-configure-the-log-messages"></a>Como configurar as mensagens de registo
Existem dois retornos de chamada de interesse para o utilizador. No exemplo abaixo, pode ver como configurar a sessão.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events--properties"></a>Eventos e propriedades

Retornos de chamada de evento fornecidos para processar registos e erros da sessão.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Especifica o nível de detalhe para os eventos receber do tempo de execução.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Esse retorno de chamada do evento fornece eventos de registo de depuração padrão.
- [Erro](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Esse retorno de chamada do evento fornece eventos de registo considerado erros pelo tempo de execução.

## <a name="diagnostics-logging-in-azure-spatial-anchors"></a>Diagnóstico de iniciar sessão âncoras espaciais do Azure

Além do modo padrão de operação para o registo descrita acima, as âncoras espaciais do Azure também tem um modo de diagnóstico que os desenvolvedores podem optar por. Captura de imagens do ambiente de diagnóstico e regista-los no disco. Esse modo é útil na depuração de certos tipos de problemas, como quando não são capazes de forma previsível localizar uma âncora. Apenas ative o diagnóstico de registo para reproduzir um problema específico e, em seguida, desativá-la. Não execute as suas aplicações normalmente com o diagnóstico ativado.

Durante uma interação de suporte com a Microsoft, um representante da Microsoft poderá pedir-se que está disposto a enviar um pacote de diagnóstico para a Microsoft para uma investigação mais aprofundada. Neste caso, poderá optar por ativar diagnósticos, reproduzir o problema e enviar o pacote de diagnóstico para a Microsoft para uma investigação mais aprofundada. Os registos de diagnóstico enviados à Microsoft sem confirmação anterior, por um representante da Microsoft passarão sem resposta.

Os fragmentos de código seguintes mostram-lhe como ativar o modo de diagnóstico e também como pode enviar registos de diagnóstico para a Microsoft.

### <a name="enabling-diagnostics-logging"></a>Ativar o registo de diagnóstico

Enquanto uma sessão está ativada para registo de diagnósticos, todas as operações na sessão terá o diagnóstico correspondente, o registo no sistema de arquivos local. Registo inclui guardar imagens do ambiente de disco.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // Opt-in to diagnostics logging of environment images.
    // If this is enabled, the diagnostics bundle will include images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submitting-the-diagnostic-bundle"></a>Enviar o pacote de diagnóstico

O fragmento de código seguinte mostra como submeter um pacote de diagnóstico para a Microsoft. Tenha em atenção que esta irá inclui imagens do ambiente capturadas pela sessão depois de ativar o diagnóstico. Além disso, os pacotes de diagnóstico enviados à Microsoft sem confirmação anterior, por um representante da Microsoft passarão sem resposta.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest  to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="anatomy-of-the-diagnostics-bundle"></a>Anatomia do pacote de diagnóstico
As seguintes informações podem estar presentes num pacote de diagnóstico:

- Imagens de quadro-chave - imagens do ambiente capturado durante a sessão enquanto a diagnóstico foram ativado.
- Registos - eventos de registo registados pelo tempo de execução.
- Metadados de sessão - metadados que identifica a sessão.
