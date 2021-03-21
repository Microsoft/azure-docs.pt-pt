---
title: Configurar o Remote Rendering para o Unity
description: Como rubricar a renderização remota do Azure num projeto de unidade
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594219"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurar o Remote Rendering para o Unity

Para permitir a renderização remota de Azure (ARR) na Unidade, fornecemos métodos dedicados que cuidam de alguns aspetos específicos da Unidade.

## <a name="startup-and-shutdown"></a>Arranque e encerramento

Para rubricar a renderização remota, utilize `RemoteManagerUnity` . Esta classe chama para o `RenderingConnection` genérico, mas já implementa detalhes específicos da unidade para si. Por exemplo, a Unidade usa um sistema de coordenadas específico. Ao `RemoteManagerUnity.Initialize` ligar, a convenção adequada será criada. A chamada também requer que forneça a câmara Unidade que deve ser usada para exibir o conteúdo prestado remotamente.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Para desligar a renderização remota, `RemoteManagerStatic.ShutdownRemoteRendering()` ligue.

Depois de ter `RenderingSession` sido criada e escolhida como a sessão de renderização primária, deve ser registada `RemoteManagerUnity` em:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Código de exemplo completo

O código abaixo demonstra todos os passos necessários para rubricar a renderização remota do Azure na Unidade:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funções de conveniência

### <a name="session-state-events"></a>Eventos do estado da sessão

`RemoteManagerUnity.OnSessionUpdate` emite eventos para quando o seu estado de sessão muda, consulte a documentação do código para mais detalhes.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` é um componente opcional para simplificar a configuração e gestão da sessão. Contém opções para interromper automaticamente a sessão quando a aplicação está a sair ou o modo de reprodução é saída no editor, bem como renovar automaticamente o arrendamento da sessão quando necessário. Caches dados como as propriedades da sessão (ver a sua `LastProperties` variável) e expõe eventos para alterações de estado de sessão e erros de sessão.

Não pode haver mais do que um caso de `ARRServiceUnity` cada vez. Destina-se a começar mais rapidamente, implementando alguma funcionalidade comum. Para uma aplicação maior, pode ser preferível fazer essas coisas por si mesmo.

Para um exemplo, como configurar e utilizar `ARRServiceUnity` ver [Tutorial: Visualização remota de modelos renderizados](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Passos seguintes

* [Instalar o pacote do Remote Rendering para o Unity](install-remote-rendering-unity-package.md)
* [Tutorial: Visualização remota de modelos renderizados](../../tutorials/unity/view-remote-models/view-remote-models.md)
