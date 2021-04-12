---
title: Quickstart - Adicione VOIP chamando a uma aplicação web usando serviços de comunicação Azure
description: Neste tutorial, você aprende a usar os serviços de comunicação Azure chamando SDK para JavaScript
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: be6ff629a651af5cc06d7928c7972f07aa0fd6e2
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107291329"
---
Neste arranque rápido, você vai aprender como iniciar uma chamada usando os Serviços de Comunicação Azure Chamando SDK para JavaScript.

> [!NOTE]
> Este documento utiliza a versão 1.0.0-beta.10 do Call SDK.


## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (8.11.1 e 10.14.1 recomendado).
- Um recurso ativo dos Serviços de Comunicação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um Token de Acesso ao Utilizador para instantaneaizar o cliente da chamada. Saiba como [criar e gerir fichas de acesso ao utilizador.](../../access-tokens.md)


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Aqui está o código:

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
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
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
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características dos Serviços de Comunicação Azure Chamando SDK:

| Nome                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | O CallClient é o principal ponto de entrada para o Call SDK.                                                                       |
| Callagent                        | O CallAgent é usado para iniciar e gerir chamadas.                                                                                            |
| AzureCommunicationTokenCredential | A classe AzureCommunicationTokenCredential implementa a interface CommunicationTokenCredential que é usada para instantaneaizar o CallAgent. |


## <a name="authenticate-the-client"></a>Autenticar o cliente

Tem de inserir um token de acesso válido do utilizador para o seu recurso no campo de texto e clicar em 'Enviar'. Consulte a documentação do [token de acesso ao utilizador](../../access-tokens.md) se ainda não tiver um token disponível. Utilizando o `CallClient` , inicialize um `CallAgent` caso com um que nos `CommunicationTokenCredential` permitirá fazer e receber chamadas. Adicione o seguinte código à **client.js:**

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Inicie uma ligação

Adicione um manipulador de eventos para iniciar uma chamada quando o `callButton` clicado:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
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
  submitToken.disabled = false;
});
```

A `forEveryone` propriedade termina a chamada para todos os participantes de chamadas.

## <a name="run-the-code"></a>Executar o código

Use o `webpack-dev-server` para construir e executar a sua aplicação. Executar o seguinte comando para agregar anfitrião de aplicações em um webserver local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o seu navegador e navegue para http://localhost:8080/ . Deverá ver o seguinte:

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="Screenshot da aplicação JavaScript concluída.":::

Pode fazer uma chamada VOIP de saída, fornecendo um ID do utilizador no campo de texto e clicando no botão **'Chamada de Início'.** Ligar `8:echo123` liga-o a um robô echo, isto é ótimo para começar e verificar se os seus dispositivos de áudio estão a funcionar.
