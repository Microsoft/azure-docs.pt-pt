---
title: Começar com componentes de base de UI dos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a começar com componentes base UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488181"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Quickstart: Começar com componentes base de ui-estruturas

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inicie-se com os Serviços de Comunicação Azure utilizando o UI Framework para integrar rapidamente experiências de comunicação nas suas aplicações. Neste arranque rápido, você vai aprender como integrar componentes base UI Framework na sua aplicação para construir experiências de comunicação. 

Os componentes do UI Framework vêm em dois sabores: Base e Compósito.

- **Os componentes de base** representam capacidades de comunicação discretas; são os blocos básicos de construção que podem ser usados para construir experiências de comunicação complexas. 
- **Os componentes compósitos** são experiências chave-na-imagem para cenários comuns de comunicação que foram construídos usando componentes base como blocos de construção e **embalados** para serem facilmente integrados em aplicações.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (Nó 12 Recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Criar um recurso de Serviços de Comunicação.](./../create-communication-resource.md)
- Um Token de Acesso ao Utilizador para instantaneaizar o cliente da chamada. Saiba como [criar e gerir fichas de acesso ao utilizador.](./../access-tokens.md)

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

//For Private Preview install tarball

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
| Fornecedor| Fornecedor fluente de UI que permite aos desenvolvedores modificar componentes de UI fluentes subjacentes|
| ChamandoProvider| Chamando o Fornecedor para instantanear uma chamada. Necessário para adicionar componentes extra|
| ChatProvider | Chat Provider para instantaneaizar um fio de chat. Necessário para adicionar componentes extra|
| MediaGallery | Componente base que mostra os participantes de chamadas e os seus streams de vídeo remotos |
| MediaControls | Componente base para controlar chamada, incluindo mudo, vídeo, ecrã de partilha |
| ChatThread | Componente base que torna um fio de chat com indicadores de dactilografia, ler recibos, etc. |
| Enviar Caixa | Componente base que permite ao utilizador inserir mensagens que serão enviadas para o fio de união|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Inicialize fornecedores de chamadas e chat usando credenciais de Serviços de Comunicação Azure

Vá à `src` pasta interior e procure o ficheiro `my-app` `app.js` . Aqui vamos deixar cair o seguinte código para rubricar os nossos fornecedores de Chamadas e Chat. Estes fornecedores são responsáveis por manter o contexto das experiências de chamada e chat. Pode escolher qual usar dependendo do tipo de experiência de comunicação que está a construir. Se necessário, pode usar os dois ao mesmo tempo. Para inicializar os componentes, você precisará de um token de acesso recuperado dos Serviços de Comunicação Azure. Para obter detalhes sobre como obter fichas de acesso, consulte: [criar e gerir fichas de acesso.](./../access-tokens.md)

> [!NOTE]
> Os componentes não geram fichas de acesso, IDs de grupo ou IDs de fio. Estes elementos provêm de serviços que passam pelos passos adequados para gerar estes IDs e passá-los para a aplicação do cliente. Para obter mais informações, consulte: [Arquitetura do Servidor do Cliente](./../../concepts/client-and-server-architecture.md).
> 
> Por exemplo: O Provedor de Chat espera que o `userId` associado ao ser utilizado para `token` inicializá-lo já tenha sido associado ao `threadId` ser fornecido. Se o símbolo não tiver sido associado ao ID do fio, então o Provedor de Chat falhará. Para mais informações sobre o chat, consulte: [Começar com o Chat](./../chat/get-started.md)

Usaremos um tema fluente de UI para melhorar o aspeto e a sensação da aplicação:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Uma vez inicializado, este provedor permite-lhe construir o seu próprio layout usando componentes de base de ui e qualquer lógica de layout extra. O provedor cuida de inicializar toda a lógica subjacente e ligar corretamente os diferentes componentes. Em seguida, usaremos vários componentes base fornecidos pela UI Framework para construir experiências de comunicação. Pode personalizar o layout destes componentes e adicionar quaisquer outros componentes personalizados que pretenda renderizar com eles. 

## <a name="build-ui-framework-calling-component-experiences"></a>Construir experiências de componente de chamada de quadro de UI

Para chamar, usaremos os `MediaGallery` `MediaControls` componentes e componentes. Para obter mais informações sobre eles, consulte [as capacidades-quadro da UI.](./../../concepts/ui-framework/ui-sdk-features.md) Para começar, na `src` pasta, crie um novo ficheiro chamado `CallingComponents.js` . Aqui vamos inicializar um componente de função que irá manter os nossos componentes base para depois importar em `app.js` . Pode adicionar layout extra e estilo em torno dos componentes. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

Na parte inferior deste ficheiro, exportamos os componentes de chamada utilizando o  `connectFuncsToContext` método a partir do Quadro de UI para ligar os componentes de UI chamando ao estado subjacente usando a função de mapeamento `MapToCallingSetupProps` . Este método produz o componente tendo os seus adereços povoados, que depois usamos para verificar o estado e aderir à chamada. Usando a `isCallInitialized` propriedade para verificar se o está pronto `CallAgent` e, em seguida, usamos o `joinCall` método para participar. O UI Framework suporta funções de mapeamento personalizado para serem usadas para cenários onde os desenvolvedores querem controlar como os dados são empurrados para os componentes.

## <a name="build-ui-framework-chat-component-experiences"></a>Construir experiências de componentes de chat de quadros de UI

Para chat, usaremos os `ChatThread` `SendBox` componentes. Para obter mais informações sobre estes componentes, consulte [as capacidades-quadro da UI](./../../concepts/ui-framework/ui-sdk-features.md). Para começar, na `src` pasta, crie um novo ficheiro chamado `ChatComponents.js` . Aqui vamos inicializar um componente de função que irá manter os nossos componentes base para depois importar em `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Adicionar componentes de chamada e chat à aplicação principal

De volta ao `app.js` ficheiro, vamos agora adicionar os componentes ao `CallingProvider` e tal como mostrado `ChatProvider` abaixo.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

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
> [Experimente componentes compósitos de quadro de UI](./get-started-with-composites.md)

Para obter mais informações, veja os seguintes recursos:
- [Visão geral do quadro da UI](../../concepts/ui-framework/ui-sdk-overview.md)
- [Capacidades-Quadro da UI](./../../concepts/ui-framework/ui-sdk-features.md)
