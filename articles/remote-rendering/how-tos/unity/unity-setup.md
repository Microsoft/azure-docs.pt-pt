---
title: Configurar a renderização remota para a unidade
description: Como inicializar a Renderização Remota Azure num projeto de Unidade
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681145"
---
# <a name="set-up-remote-rendering-for-unity"></a>Configurar a renderização remota para a unidade

Para permitir a renderização remota azure (ARR) em Unidade, fornecemos métodos dedicados que cuidam de alguns aspetos específicos da Unidade.

## <a name="startup-and-shutdown"></a>Arranque e encerramento

Para inicializar a renderização remota, utilize `RemoteManagerUnity`. Esta classe chama `RemoteManager` para o genérico, mas já implementa detalhes específicos da Unidade para si. Por exemplo, a Unidade usa um sistema de coordenadas específico. Ao `RemoteManagerUnity.Initialize`ligar, será criada a convenção adequada. A chamada também requer que forneça a câmara Unidade que deve ser utilizada para exibir o conteúdo renderizado remotamente.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Para desligar a renderização `RemoteManagerStatic.ShutdownRemoteRendering()`remota, ligue.

Depois `AzureSession` de ter sido criado e escolhido como a sessão `RemoteManagerUnity`de renderização primária, deve ser registado com:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Código de exemplo completo

O código abaixo demonstra todos os passos necessários para inicializar a renderização remota azure em unidade:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Funções de conveniência

### <a name="session-state-events"></a>Eventos estatais de sessão

`RemoteManagerUnity.OnSessionUpdate`emite eventos para quando a sua sessão de estado mudar, consulte a documentação do código para detalhes.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`é um componente opcional para agilizar a configuração e a gestão da sessão. Contém opções para parar automaticamente a sua sessão quando a aplicação está a sair ou o modo de reprodução é exited no editor, bem como renovar automaticamente o aluguer de sessão quando necessário. Caches dados como as propriedades da `LastProperties` sessão (ver a sua variável), e expõe eventos para alterações de estado de sessão e erros de sessão.

Não pode haver mais do `ARRServiceUnity` que um exemplo de cada vez. Destina-se a começar mais rápido implementando alguma funcionalidade comum. Para uma aplicação maior, pode ser preferível fazer essas coisas por si mesmo, no entanto.

Por exemplo, como configurar `ARRServiceUnity` e usar ver [Tutorial: Criar um projeto de Unidade do zero](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>Passos seguintes

* [Instale o pacote de renderização remota para a unidade](install-remote-rendering-unity-package.md)
* [Tutorial: Criação de um projeto de unidade do zero](../../tutorials/unity/project-setup.md)
