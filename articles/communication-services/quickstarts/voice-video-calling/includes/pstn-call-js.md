---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: df8d3bb5986fd31f4a9079b6e6e077126867dbfe
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "104729503"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um número de telefone adquirido no seu recurso de Serviços de Comunicação. [como obter um número de telefone.](../../telephony-sms/get-phone-number.md)
- A `User Access Token` para ativar o cliente de chamada. Para mais informações sobre [como obter um `User Access Token` ](../../access-tokens.md)


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
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
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

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential('<USER ACCESS TOKEN with PSTN scope>');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callPhoneButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>Inicie uma ligação para o telefone.

Especifique o número de telefone adquirido no recurso Serviços de Comunicação, que será usado para iniciar a chamada:
> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +122233344444)

Adicione um manipulador de eventos para iniciar uma chamada para o número de telefone que forneceu quando o `callPhoneButton` é clicado:


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.startCall(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Termine uma ligação para o telefone

Adicione um ouvinte de eventos para terminar a chamada atual quando o `hangUpPhoneButton` clicado:

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

A `forEveryone` propriedade termina a chamada para todos os participantes de chamadas.

## <a name="run-the-code"></a>Executar o código

Use o `webpack-dev-server` para construir e executar a sua aplicação. Executar o seguinte comando para agregar o anfitrião de aplicações num webserver local:


```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o seu navegador e navegue para `http://localhost:8080/` . Deverá ver o seguinte:

:::image type="content" source="../media/javascript/pstn-calling-javascript-app.png" alt-text="Screenshot da aplicação JavaScript concluída.":::

Pode fazer uma chamada para um número de telefone real, fornecendo um número de telefone no campo de texto adicionado e clicando no botão **Iniciar chamada telefónica.**

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +122233344444)
