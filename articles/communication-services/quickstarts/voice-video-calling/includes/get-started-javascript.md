---
title: Quickstart - Adicione VOIP chamando a uma aplicação web usando serviços de comunicação Azure
description: Neste tutorial, você aprende a usar os Serviços de Comunicação Azure Chamando a biblioteca de clientes para Javascript
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d58b4d86936c56a08f27bef59edc1d3cc4ce4617
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90948346"
---
Neste arranque rápido, você vai aprender como iniciar uma chamada usando a biblioteca de clientes Azure Communication Services Call para JavaScript.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (8.11.1 e 10.14.1 recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um Token de Acesso ao Utilizador para instantaneaizar o cliente da chamada. Saiba como [criar e gerir fichas de acesso ao utilizador.](../../access-tokens.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Corra `npm init -y` para criar umapackage.js** no** ficheiro com definições predefinidos.

```console
npm init -y
```

### <a name="install-the-package"></a>Instale o pacote

Utilize o `npm install` comando para instalar a biblioteca de clientes Azure Communication Services Call para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Este quickstart usa webpack para agregar os ativos da aplicação. Executar o seguinte comando para instalar os pacotes npm webpack, webpack-cli e webpack-dev-server e listá-los como dependências de desenvolvimento no seu **package.jsem**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Crie um ** ficheiroindex.html** no diretório de raiz do seu projeto. Usaremos este ficheiro para configurar um layout básico que permitirá ao utilizador fazer uma chamada para um Bot de Comunicações Azure.

Apresentamos o código a seguir:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Crie um ficheiro no diretório de raiz do seu projeto chamado **client.js** para conter a lógica de aplicação para este arranque rápido. Adicione o seguinte código para importar o cliente de chamada e obter referências aos elementos DOM para que possamos anexar a nossa lógica de negócio. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");

// quickstart code goes here
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes Azure Communication Services Call:

| Nome                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | O CallClient é o principal ponto de entrada para a biblioteca do cliente Call.                                                                       |
| Callagent                        | O CallAgent é usado para iniciar e gerir chamadas.                                                                                            |
| AzureCommunicationUserCredential | A classe AzureCommunicationUserCredential implementa a interface CommunicationUserCredential que é usada para instantaneaizar o CallAgent. |


## <a name="authenticate-the-client"></a>Autenticar o cliente

Tem de ser substituído `<USER_ACCESS_TOKEN>` por um token de acesso válido do utilizador para o seu recurso. Consulte a documentação do [token de acesso ao utilizador](../../access-tokens.md) se ainda não tiver um token disponível. Utilizando o `CallClient` , inicialize um `CallAgent` caso com um que nos `CommunicationUserCredential` permitirá fazer e receber chamadas. Adicione o seguinte código à **client.js: **

```javascript
const callClient = new CallClient();
const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
let callAgent;

callClient.createCallAgent(tokenCredential).then(agent => {
  callAgent = agent;
  callButton.disabled = false;
});
```

## <a name="start-a-call"></a>Inicie uma ligação

Adicione um manipulador de eventos para iniciar uma chamada quando o `callButton` clicado:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Termine uma chamada

Adicione um ouvinte de eventos para terminar a chamada atual quando o `hangUpButton` clicado:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

A `forEveryone` propriedade termina a chamada para todos os participantes de chamadas.

## <a name="run-the-code"></a>Executar o código

Use o `webpack-dev-server` para construir e executar a sua aplicação. Executar o seguinte comando para agregar anfitrião de aplicações em um webserver local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o seu navegador e navegue para http://localhost:8080/ . Deverá ver o seguinte:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Screenshot da aplicação JavaScript concluída.":::

Pode fazer uma chamada VOIP de saída, fornecendo um ID do utilizador no campo de texto e clicando no botão **'Chamada de Início'.** Ligar `8:echo123` liga-o a um robô echo, isto é ótimo para começar e verificar se os seus dispositivos de áudio estão a funcionar.
