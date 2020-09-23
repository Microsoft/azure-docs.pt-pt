---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: cdd4988f9a23904c0771852c4539aa9bce2ee683
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948347"
---
Inicie-se com os Serviços de Comunicação Azure utilizando a biblioteca de clientes SMS javaScript dos Serviços de Comunicação para enviar mensagens SMS.

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

Corra `npm init -y` para criar umapackage.js** no** ficheiro com definições predefinidos.

```console
npm init -y
```

Use um editor de texto para criar um ficheiro chamado **send-sms.js** no diretório de raiz do projeto. Irá adicionar todo o código fonte para este arranque rápido a este ficheiro nas seguintes secções.

### <a name="install-the-package"></a>Instale o pacote

Utilize o `npm install` comando para instalar a biblioteca de clientes Azure Communication Services SMS para o JavaScript.

```console
npm install @azure/communication-sms --save
```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características da biblioteca de clientes dos Serviços de Comunicação Azure para Node.js.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Esta classe é necessária para toda a funcionalidade SMS. Você instantaneamente com as suas informações de subscrição, e use-as para enviar mensagens SMS. |
| Envios Opções | Esta interface fornece opções para configurar relatórios de entrega. Se `enable_delivery_report` estiver programado para , `true` então um evento será emitido quando a entrega foi bem sucedida. |
| SendMessageRequest | Esta interface é o modelo para a construção do pedido de SMS (por exemplo. configurar o de e para os números de telefone e o conteúdo de SMS). |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Importe o **SmsClient** da biblioteca do cliente e o instantie com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código à **send-sms.js: **

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS

Envie uma mensagem SMS ligando para o `send` método. Adicione este código ao fim do **send-sms.js: **

```javascript
await smsClient.send({
  from: "<leased-phone-number>",
  to: ["<to-phone-number>"],
  message: "Hello World 👋🏻 via Sms"
}, {
  enableDeliveryReport: true //Optional parameter
});
```

Deverá substituir `<leased-phone-number>` por um número de telefone por SMS associado ao seu recurso de Serviços de Comunicação e `<to-phone-number>` pelo número de telefone a que deseja enviar uma mensagem. Todos os parâmetros de número de telefone devem aderir à [norma E.164](../../../concepts/telephony-sms/plan-solution.md#optional-reading-international-public-telecommunication-numbering-plan-e164).

O `enableDeliveryReport` parâmetro é um parâmetro opcional que pode utilizar para configurar relatórios de entrega. Isto é útil para cenários em que pretende emitir eventos quando as mensagens SMS são entregues. Consulte o [Punho SMS Events](../handle-sms-events.md) quickstart para configurar relatórios de entrega para as suas mensagens SMS.

## <a name="run-the-code"></a>Executar o código

Utilize o `node` comando para executar o código que adicionou ao ficheiro **send-sms.js.**

```console

node ./send-sms.js

```