---
title: APIs frontend azure para autenticação
description: Explica como usar a API frontal C# para autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681353"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Utilize as APIs Frontend Azure para autenticação

Nesta secção, descreveremos como utilizar a API C# para autenticação.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

O AzureFrontendAccountInfo é utilizado para configurar ```AzureFrontend``` as informações de autenticação, por exemplo, no SDK.

Os campos importantes são:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

Para a _região_ que faz parte do domínio, utilize uma [região perto de si.](../reference/regions.md)

A informação da conta pode ser obtida a partir do portal, conforme descrito no parágrafo de informação da [conta de recuperação.](create-an-account.md#retrieve-the-account-information)

## <a name="azure-frontend"></a>Azure Frontend

As classes ```AzureFrontend``` relevantes são e. ```AzureSession``` ```AzureFrontend```é utilizado para a gestão de conta e funcionalidade de nível de conta, que inclui: conversão de ativos e criação de sessão de renderização. ```AzureSession```é utilizado para a funcionalidade de nível de sessão e inclui: atualização da sessão, consultas, renovação e desmantelamento.

Cada um ```AzureSession``` aberto/criado manterá uma referência à frente que a criou. Para encerrar de forma limpa, todas as sessões devem ser negociadas antes que a frente seja negociada.

A colocação de uma sessão não vai `AzureSession.StopAsync` parar o VM em Azure, deve ser explicitamente chamado.

Uma vez criada uma sessão e o seu estado foi marcado como pronto, pode ligar-se ao tempo de funcionamento da renderização remota com `AzureSession.ConnectToRuntime`.

### <a name="threading"></a>Rosca

Todas as chamadas de asincronização AzureSession e AzureFrontend são completadas num fio de fundo, e não no fio principal da aplicação.

### <a name="conversion-apis"></a>APIs de conversão

Para obter mais informações sobre o serviço de conversão, consulte [a conversão do modelo REST API](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Iniciar a conversão de ativos

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

#### <a name="get-conversion-status"></a>Obter estado de conversão

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

### <a name="rendering-apis"></a>ApIs de renderização

Consulte [a gestão da sessão REST API](session-rest-api.md) para obter detalhes sobre a gestão da sessão.

Uma sessão de renderização pode ser criada dinamicamente no serviço ou um ID de sessão já existente pode ser 'aberto' em um objeto AzureSession.

#### <a name="create-rendering-session"></a>Criar sessão de renderização

``` cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="open-an-existing-rendering-session"></a>Abra uma sessão de renderização existente

Abrir uma sessão existente é uma chamada sincronizada.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Obtenha sessões de renderização atuais

``` cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

### <a name="session-apis"></a>Sessão APIs

#### <a name="get-rendering-session-properties"></a>Obtenha propriedades de sessão de renderização

``` cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="update-rendering-session"></a>Sessão de renderização de atualização

``` cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="stop-rendering-session"></a>Pare de renderizar a sessão

``` cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

#### <a name="connect-to-arr-inspector"></a>Ligue-se ao inspetor da ARR

``` cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta](create-an-account.md)
* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
