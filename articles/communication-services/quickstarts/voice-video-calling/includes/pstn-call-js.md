---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 009bd57fdb82b8463352da8dc63c9aeebceab09b
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779683"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Um recurso de Serviços de Comunicação implantado. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um número de telefone adquirido em recursos dos Serviços de Comunicação. [como obter um número de telefone.](../../telephony-sms/get-phone-number.md)
- A `User Access Token` para ativar o cliente de chamada. Para mais informações sobre [como obter um `User Access Token` ](../../access-tokens.md)
- Complete o quickstart para [começar com a adição de chamadas à sua aplicação](../getting-started-with-calling.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Para visualizar os números de telefone associados ao seu recurso serviços de comunicação, inscreva-se no [portal Azure,](https://portal.azure.com/)localize o seu recurso de Serviços de Comunicação e abra o separador **números** de telefone a partir do painel de navegação à esquerda.
- Pode construir e executar a sua aplicação com serviços de comunicação Azure Chamando a biblioteca de clientes para JavaScript:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

## <a name="setting-up"></a>Configuração

### <a name="add-pstn-functionality-to-your-app"></a>Adicione a funcionalidade PSTN à sua aplicação

Estenda o seu layout com controlos de marcação de telefone.

Coloque este código no fim da `<body />` secção de **index.html,** antes `<script />` das etiquetas:

```html
<input 
  id="callee-phone-input"
  type="text"
  placeholder="Phone number you would like to dial"
  style="margin-bottom:1em; width: 230px;"
/>
<div>
  <button id="call-phone-button" type="button">
    Start Phone Call
  </button>
  &nbsp;
  <button id="hang-up-phone-button" type="button" disabled="true">
    Hang Up Phone Call
  </button>
</div>
```

Estenda a sua lógica de aplicação com a funcionalidade de telefonia.

Adicione este código a **client.js: **

```javascript
const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");
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
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: '+18336528005'}
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
npx webpack-dev-server --entry ./client.js --output bundle.js
```

Abra o seu navegador e navegue para `http://localhost:8080/` . Deverá ver o seguinte:


![Screenshot da aplicação JavaScript concluída.](../media/javascript/pstn-calling-javascript-app.png)

Pode fazer uma chamada para um número de telefone real, fornecendo um número de telefone no campo de texto adicionado e clicando no botão **Iniciar chamada telefónica.**

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +122233344444)
