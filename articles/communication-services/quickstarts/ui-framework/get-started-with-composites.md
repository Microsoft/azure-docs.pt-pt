---
title: Começar com componentes compósitos Azure Communication Services UI Framework SDK
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a começar com uI Framework Composite Components
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7356fb90914e948b6a74a478ce1e19722b224346
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488062"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Quickstart: Começar com componentes compósitos de ui framework

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inicie-se com os Serviços de Comunicação Azure utilizando o UI Framework para integrar rapidamente experiências de comunicação nas suas aplicações. Neste arranque rápido, você vai aprender como integrar os Componentes Compostos De UI Framework na sua aplicação para construir experiências de comunicação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (Nó 12 Recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Criar um recurso de Serviços de Comunicação.](./../create-communication-resource.md)
- Um Token de Acesso ao Utilizador para instantaneaizar o composto de chamada. Saiba como [criar e gerir fichas de acesso ao utilizador.](./../access-tokens.md)

## <a name="setting-up"></a>Configuração

O UI Framework requer uma configuração de um ambiente de Reação. A seguir, vamos fazê-lo. Se já tem uma App React, pode saltar esta secção.

### <a name="set-up-react-app"></a>Configurar app react

Usaremos o modelo de aplicação create-react para este arranque rápido. Para mais informações, consulte: [Começar com Reagir](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

No final deste processo, deverá ter uma aplicação completa dentro da pasta `my-app` . Para este arranque rápido, vamos modificar ficheiros dentro da `src` pasta.

### <a name="install-the-package"></a>Instale o pacote

Utilize o `npm install` comando para instalar a biblioteca de clientes Azure Communication Services Call para JavaScript. Mova o tarball fornecido (Pré-visualização Privada) para o diretório da minha aplicação.

```console

//Private Preview install tarball

npm install --save ./{path for tarball}

```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

### <a name="run-create-react-app"></a>Executar Criar Aplicação reagir

Vamos testar a instalação da App Create React executando:

```console

npm run start 

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes da UI dos Serviços de Comunicação Azure:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| GroupCall | Componente compósito que torna uma experiência de chamada de grupo com galeria e controlos participantes. |
| GroupChat | Componente composto que torna uma experiência de chat de grupo com fio de chat e entrada |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Inicialize a chamada de grupo e os componentes compostos de chat de grupo

Vá à `src` pasta interior e procure o ficheiro `my-app` `app.js` . Aqui vamos deixar cair o seguinte código para rubricar os nossos Componentes Compostos para Chat e Chamada de Grupo. Pode escolher qual usar dependendo do tipo de experiência de comunicação que está a construir. Se necessário, pode usar os dois ao mesmo tempo. Para inicializar os componentes, você precisará de um token de acesso recuperado dos Serviços de Comunicação Azure. Para obter detalhes sobre como obter fichas de acesso, consulte: [criar e gerir fichas de acesso ao utilizador.](./../access-tokens.md)

> [!NOTE]
> Os componentes não geram fichas de acesso, IDs de grupo ou IDs de fio. Estes elementos provêm de serviços que passam pelos passos adequados para gerar estes IDs e passá-los para a aplicação do cliente. Para obter mais informações, consulte: [Arquitetura do Servidor do Cliente](./../../concepts/client-and-server-architecture.md).
> 
> Por exemplo: O compósito do Group Chat espera que `userId` o associado ao ser utilizado para `token` inicializá-lo já tenha sido associado ao `threadId` ser fornecido. Se o símbolo não tiver sido associado ao ID do fio, então o compósito do Group Chat falhará. Para mais informações sobre o chat, consulte: [Começar com o Chat](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>Executar quickstart

Para executar o código acima, utilize o comando:

```console

npm run start 

```

Para testar totalmente as capacidades, precisará de um segundo cliente com funcionalidade de chamada e chat para se juntar à linha de chamada e chat. Consulte a nossa [amostra de herói chamando](./../../samples/calling-hero-sample.md) e [a amostra de herói de chat](./../../samples/chat-hero-sample.md) como opções potenciais.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Experimente componentes de base-quadro da UI](./get-started-with-components.md)

Para obter mais informações, veja os seguintes recursos:
- [Visão geral do quadro da UI](../../concepts/ui-framework/ui-sdk-overview.md)
- [Capacidades-Quadro da UI](./../../concepts/ui-framework/ui-sdk-features.md)
