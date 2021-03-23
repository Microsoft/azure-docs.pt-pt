---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 913f2bc5569dc9bdd058c38902ad0c99acee5d48
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803448"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Use um editor de texto para criar um ficheiro chamado **issue-access-tokens.py** no diretório de raiz do projeto e adicione a estrutura para o programa, incluindo o manuseamento básico de exceções. Irá adicionar todo o código fonte para este arranque rápido a este ficheiro nas seguintes secções.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes de identidade de serviços de comunicação Azure para pacote Python utilizando o `pip install` comando.

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiizar a `CommunicationIdentityClient` com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../create-communication-resource.md#store-your-connection-string)

Adicione este código dentro do `try` bloco:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Em alternativa, se tiver gerido a configuração da identidade, consulte [identidades geridas de utilização,](../managed-identity.md)pode também autenticar com identidade gerida.
```python
const endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"];
var client = new CommunicationIdentityClient(endpoint, DefaultAzureCredential());
```

## <a name="create-an-identity"></a>Criar uma identidade

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `create_user` método para criar uma nova entrada no diretório com um único `Id` . A loja recebeu identidade com mapeamento para os utilizadores da sua aplicação. Por exemplo, armazenando-os na base de dados do seu servidor de aplicações. A identidade é necessária mais tarde para emitir fichas de acesso.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>Emitir fichas de acesso

Utilize o `get_token` método para emitir um token de acesso para a identidade dos Serviços de Comunicação já existentes. O parâmetro `scopes` define um conjunto de primitivos que autorizarão este token de acesso. Consulte a [lista de ações apoiadas.](../../concepts/authentication.md) Nova instância de parâmetro `CommunicationUserIdentifier` pode ser construída com base na representação de cordas da identidade do Serviço de Comunicação Azure.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Os tokens de acesso são credenciais de curta duração que precisam de ser reeditadas. Não fazê-lo pode causar perturbações na experiência dos utilizadores da sua aplicação. A `expires_on` propriedade de resposta indica a vida útil do token de acesso.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Criar uma identidade e emitir um token de acesso dentro do mesmo pedido

Utilize o `create_user_and_token` método para criar uma identidade dos Serviços de Comunicação e emita um símbolo de acesso para o mesmo. O parâmetro `scopes` define um conjunto de primitivos que autorizarão este token de acesso. Consulte a [lista de ações apoiadas.](../../concepts/authentication.md)

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Atualizar tokens de acesso

Para refrescar um token de acesso, use o `CommunicationUserIdentifier` objeto para reeditar:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Revogar fichas de acesso

Em alguns casos, pode revogar explicitamente os tokens de acesso. Por exemplo, quando o utilizador de uma aplicação altera a palavra-passe que utiliza para autenticar o seu serviço. O método `revoke_tokens` invalida todos os tokens de acesso ativo, que foram emitidos para a identidade.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Eliminar uma identidade

A eliminação de uma identidade revoga todos os tokens de acesso ativo e impede-o de emitir fichas de acesso para a identidade. Também remove todo o conteúdo persistido associado à identidade.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Executar o código

A partir de um pedido de consola, navegue até ao diretório que contém o ficheiro *issue-access-tokens.py* e, em seguida, execute o seguinte `python` comando para executar a aplicação.

```console
python ./issue-access-tokens.py
```
