---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: c1c6478948aaf207f0ca1adf367840ca3db34649
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325305"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versões LTS e Manutenção ativas LTS (8.11.1 e 10.14.1 recomendado).
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Corra `npm init -y` para criar umapackage.js **no** ficheiro com definições predefinidos.

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
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Guarde o novo ficheiro à medida **queissue-access-token.js** no *diretório de acesso-tokens-quickstart.*

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar a `CommunicationIdentityClient` com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao método `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Criar uma identidade

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `createUser` método para criar uma nova entrada no diretório com um único `Id` . A loja recebeu identidade com mapeamento para os utilizadores da sua aplicação. Por exemplo, armazenando-os na base de dados do seu servidor de aplicações. A identidade é necessária mais tarde para emitir fichas de acesso.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Emitir fichas de acesso

Utilize o `issueToken` método para emitir um token de acesso para a identidade dos Serviços de Comunicação já existentes. O parâmetro `scopes` define um conjunto de primitivos que autorizarão este token de acesso. Consulte a [lista de ações apoiadas.](../../concepts/authentication.md) Nova instância de parâmetro `communicationUser` pode ser construída com base na representação de cordas da identidade do Serviço de Comunicação Azure.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Os tokens de acesso são credenciais de curta duração que precisam de ser reeditadas. Não fazê-lo pode causar perturbações na experiência dos utilizadores da sua aplicação. A `expiresOn` propriedade de resposta indica a vida útil do token de acesso.


## <a name="refresh-access-tokens"></a>Atualizar tokens de acesso

Para refrescar um token de acesso, use o `CommunicationUser` objeto para reeditar:

```javascript  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identityResponse = new CommunicationUser(existingIdentity);
tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Revogar fichas de acesso

Em alguns casos, pode revogar explicitamente os tokens de acesso. Por exemplo, quando o utilizador de uma aplicação altera a palavra-passe que utiliza para autenticar o seu serviço. O método `revokeTokens` invalida todos os tokens de acesso ativo, que foram emitidos para a identidade.

```javascript  
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Eliminar uma identidade

A eliminação de uma identidade revoga todos os tokens de acesso ativo e impede-o de emitir fichas de acesso para a identidade. Também remove todo o conteúdo persistido associado à identidade.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Executar o código

A partir de um pedido de consola, navegue até ao diretório que contém o ficheiro *issue-access-token.js* e, em seguida, execute o seguinte `node` comando para executar a aplicação.

```console
node ./issue-access-token.js
```
