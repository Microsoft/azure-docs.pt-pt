---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 8fe8b853fe07af40603950a61c0dd2a1df74d14e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644347"
---
Inicie-se com os Serviços de Comunicação Azure utilizando os Serviços de Comunicação JavaScript SMS SDK para enviar mensagens SMS.

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (8.11.1 e 10.14.1 recomendado).
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)
- Um número de telefone por SMS habilitado. [Obter um número de telefone.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Numa janela de terminal ou de comando, corra `node --version` para verificar se Node.js está instalada.
- Para visualizar os números de telefone associados ao seu recurso serviços de comunicação, inscreva-se no [portal Azure,](https://portal.azure.com/)localize o seu recurso de Serviços de Comunicação e abra o separador **números** de telefone a partir do painel de navegação à esquerda.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Primeiro, abra o seu terminal ou janela de comando, crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Corra `npm init -y` para criar umapackage.js **no** ficheiro com definições predefinidos.

```console
npm init -y
```

Use um editor de texto para criar um ficheiro chamado **send-sms.js** no diretório de raiz do projeto. Irá adicionar todo o código fonte para este arranque rápido a este ficheiro nas seguintes secções.

### <a name="install-the-package"></a>Instale o pacote

Utilize o `npm install` comando para instalar o SDK dos Serviços de Comunicação Azure para o JavaScript.

```console
npm install @azure/communication-sms --save
```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Azure Communication Services SMS SDK para Node.js.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Esta classe é necessária para toda a funcionalidade SMS. Você instantaneamente com as suas informações de subscrição, e use-as para enviar mensagens SMS. |
| SmsSendRequest | Esta interface é o modelo para a construção do pedido de SMS (por exemplo. configurar o de e para os números de telefone e o conteúdo de SMS). |
| Opções SmsSend | Esta interface fornece opções para configurar relatórios de entrega. Se `enableDeliveryReport` estiver programado para , `true` então um evento será emitido quando a entrega for bem sucedida. |
| SmsSendResult               | Esta classe contém o resultado do serviço SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Importe o **SmsClient** do SDK e o instantie com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação do seu recurso.](../../create-communication-resource.md#store-your-connection-string)

Crie e abra um ficheiro nomeado **send-sms.js** e adicione o seguinte código:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Envie uma mensagem SMS 1:N

Para enviar uma mensagem SMS para uma lista de destinatários, ligue para a `send` função do SmsClient com uma lista de números de telefone dos destinatários (se desejar enviar uma mensagem a um único destinatário, inclua apenas um número na lista). Adicione este código ao fim de **send-sms.js:**

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu recurso de Serviços de Comunicação `<to-phone-number-1>` e pelos `<to-phone-number-2>` números de telefone a que deseja enviar uma mensagem.

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Envie uma mensagem SMS 1:N com opções

Pode também passar por um objeto de opções para especificar se o relatório de entrega deve ser ativado e definir etiquetas personalizadas.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Deverá substituir `<from-phone-number>` por um número de telefone por SMS associado ao seu recurso de Serviços de Comunicação `<to-phone-number-1>` e por `<to-phone-number-2>` números de telefone para o quais deseja enviar uma mensagem.

> [!WARNING]
> Note que os números de telefone devem ser fornecidos no formato padrão E.164. (por exemplo: +14255550123).

O `enableDeliveryReport` parâmetro é um parâmetro opcional que pode utilizar para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.
`tag` é um parâmetro opcional que pode usar para aplicar uma etiqueta no Relatório de Entrega.

## <a name="run-the-code"></a>Executar o código

Utilize o `node` comando para executar o código que adicionou ao ficheiro **send-sms.js.**

```console

node ./send-sms.js

```
