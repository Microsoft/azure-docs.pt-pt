---
title: Crie o seu próprio componente de Enquadramento de UI
titleSuffix: An Azure Communication Services quickstart
description: Neste arranque rápido, você vai aprender a construir um componente personalizado compatível com o UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 093fcfd95d291d959ed49cc39a227a99f14a0383
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488249"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Quickstart: Crie o seu componente-quadro de UI próprio

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Inicie-se com os Serviços de Comunicação Azure utilizando o UI Framework para integrar rapidamente experiências de comunicação nas suas aplicações.

Neste arranque rápido, você vai aprender como criar os seus próprios componentes usando a interface de estado pré-definida oferecida pela UI Framework. Esta abordagem é ideal para desenvolvedores que precisam de mais personalização e querem usar os seus próprios ativos de design para a experiência. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (Nó 12 Recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Criar um recurso de Serviços de Comunicação.](./../create-communication-resource.md)
- Um Token de Acesso ao Utilizador para instantaneaizar o cliente da chamada. Saiba como [criar e gerir fichas de acesso ao utilizador.](./../access-tokens.md)

O UI Framework requer a criação de um ambiente de reação. A seguir, vamos fazê-lo. Se já tem uma App React, pode saltar esta secção.

### <a name="set-up-react-app"></a>Configurar app react

Usaremos o modelo de aplicação create-react para este arranque rápido. Para mais informações, consulte: [Começar com Reagir](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

No final deste processo deverá ter uma aplicação completa dentro da pasta `my-app` . Para este arranque rápido, vamos modificar ficheiros dentro da `src` pasta.

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
| ChamandoProvider| Chamando o Fornecedor para instantanear uma chamada. Necessário para adicionar componentes de base|
| ChatProvider | Chat Provider para instantaneaizar um fio de chat. Necessário para adicionar componentes de base|
| connectFuncsToContext | Método para ligar componentes do UI Framework com fornecedores subjacentes usando mappers |
| MapToChatMessageProps | Mapper de camada de dados de mensagem de chat que fornece componentes com adereços de mensagem de chat |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Inicialize fornecedores de chat usando credenciais de Serviços de Comunicação Azure

Para este arranque rápido, usaremos o chat como exemplo, para obter mais informações sobre chamadas, consulte [os componentes base Quickstart](./get-started-with-components.md) e [Compósitos Quickstart](./get-started-with-composites.md).

Vá à `src` pasta interior e procure o ficheiro `my-app` `app.js` . Aqui vamos deixar cair o seguinte código para rubricar o nosso Provedor de Chat. Este fornecedor é responsável pela manutenção do contexto das experiências de chamada e chat. Para inicializar os componentes, você precisará de um token de acesso recuperado dos Serviços de Comunicação Azure. Para obter detalhes sobre como fazer obter um token de acesso, consulte: [criar e gerir fichas de acesso.](./../access-tokens.md)

Os Componentes-Quadro da UI seguem a mesma arquitetura geral para o resto do serviço. Os componentes não geram fichas de acesso, IDs de grupo ou IDs de fio. Estes elementos provêm de serviços que passam pelos passos adequados para gerar estes IDs e passá-los para a aplicação do cliente. Para mais informações, consulte [a Arquitetura do Servidor do Cliente.](./../../concepts/client-and-server-architecture.md)

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Uma vez inicializado, este provedor permite-lhe construir o seu próprio layout usando o Componente De Estrutura ui e qualquer lógica de layout extra. O provedor cuida de inicializar toda a lógica subjacente e ligar corretamente os diferentes componentes. Em seguida, criaremos um componente personalizado usando mappers UI Framework para conectar ao nosso provedor de chat.


## <a name="create-a-custom-component-using-mappers"></a>Criar um componente personalizado usando mappers

Começaremos por criar um novo ficheiro chamado `SimpleChatThread.js` onde iremos criar o componente. Começaremos por importar os componentes-quadro da UI de que precisamos. Aqui, usaremos fora da caixa html e reagiremos para criar um componente totalmente personalizado para um simples fio de chat. Usando o `connectFuncsToContext` método, usaremos o `MapToChatMessageProps` mapear para mapear adereços para  `SimpleChatThread` componentes personalizados. Estes adereços dar-nos-ão acesso às mensagens de chat que estão a ser enviadas e recebidas para as povoar no nosso simples fio.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Adicione o seu componente personalizado à sua aplicação

Agora que temos o nosso componente personalizado pronto, vamos importá-lo e adicioná-lo ao nosso layout.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Executar quickstart

Para executar o código acima, utilize o comando:

```console

npm run start   

```

Para testar totalmente as capacidades, precisará de um segundo cliente com funcionalidade de chat para enviar mensagens que serão recebidas pela nossa Simple Chat Thread. Consulte a nossa [amostra de herói chamando](./../../samples/calling-hero-sample.md) e [a amostra de herói de chat](./../../samples/chat-hero-sample.md) como opções potenciais.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma assinatura de Serviços de Comunicação, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados. Saiba mais sobre [a limpeza de recursos.](../create-communication-resource.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Experimente componentes compósitos de quadro de UI](./get-started-with-composites.md)

Para obter mais informações, veja os seguintes recursos:
- [Visão geral do quadro da UI](../../concepts/ui-framework/ui-sdk-overview.md)
- [Capacidades-Quadro da UI](./../../concepts/ui-framework/ui-sdk-features.md)
- [UI Framework Base Components Quickstart](./get-started-with-components.md)
