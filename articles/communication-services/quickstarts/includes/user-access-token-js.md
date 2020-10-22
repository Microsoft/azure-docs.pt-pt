---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 22cfe369561eab1ca334c7ff2450162dfae3e761
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347059"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (8.11.1 e 10.14.1 recomendado).
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Corra `npm init -y` para criar umapackage.js** no** ficheiro com definições predefinidos.

```console
npm init -y
```

### <a name="install-the-package"></a>Instale o pacote

Utilize o `npm install` comando para instalar a biblioteca de clientes da Administração de Serviços de Comunicação Azure para o JavaScript.

```console

npm install @azure/communication-administration --save

```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

## <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abra um novo ficheiro de texto no seu editor de código
1. Adicione uma `require` chamada para carregar o `CommunicationIdentityClient`
1. Criar a estrutura para o programa, incluindo o manuseamento básico de exceções

Utilize o seguinte código para começar:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Guarde o novo ficheiro à medida **queissue-token.js** no *diretório de arranque rápido do utilizador.*

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar a `CommunicationIdentityClient` com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao método `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Criar um utilizador

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `createUser` método para criar uma nova entrada no diretório com um único `Id` . Deverá manter um mapeamento entre os utilizadores da sua aplicação e os Serviços de Comunicação gerados (por exemplo, armazenando-os na base de dados do seu servidor de aplicações).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Emitir fichas de acesso ao utilizador

Utilize o `issueToken` método para emitir um token de acesso para um utilizador dos Serviços de Comunicação. Se não fornecer o `user` parâmetro opcional, um novo utilizador será criado e devolvido com o token.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Os tokens de acesso ao utilizador são credenciais de curta duração que precisam de ser reemitidas de forma a evitar que os seus utilizadores experimentem perturbações de serviço. A `expiresOn` propriedade de resposta indica a vida útil do símbolo.

## <a name="revoke-user-access-tokens"></a>Revogar fichas de acesso ao utilizador

Em alguns casos, poderá ser necessário revogar explicitamente as fichas de acesso ao utilizador, por exemplo, quando um utilizador altera a palavra-passe que utiliza para autenticar o seu serviço. Isto utiliza o `revokeTokens` método para invalidar todos os tokens de acesso de um utilizador.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="refresh-user-access-tokens"></a>Atualizar fichas de acesso ao utilizador

Para refrescar um token, use o `CommunicationUser` objeto para reeditir:

```javascript  
let userResponse = new CommunicationUser(existingUserId);
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
```

## <a name="delete-a-user"></a>Eliminar um utilizador

A eliminação de um utilizador revoga todos os tokens ativos e impede-o de emitir fichas subsequentes para as identidades. Também remove todo o conteúdo persistido associado ao utilizador.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Executar o código

A partir de um pedido de consola, navegue até ao diretório que contém o ficheiro *issue-token.js* e, em seguida, execute o seguinte `node` comando para executar a aplicação.

```console
node ./issue-token.js
```
