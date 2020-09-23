---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948155"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python](https://www.python.org/downloads/) 2.7, 3.5, ou acima.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. Abra o seu terminal ou janela de comando crie um novo diretório para a sua aplicação e navegue até ele.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Use um editor de texto para criar um ficheiro chamado **issue-tokens.py** no diretório de raiz do projeto e adicione a estrutura para o programa, incluindo o manuseamento básico de exceções. Irá adicionar todo o código fonte para este arranque rápido a este ficheiro nas seguintes secções.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes da Azure Communication Services Administration para o pacote Python utilizando o `pip install` comando.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

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

## <a name="create-a-user"></a>Criar um utilizador

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `create_user` método para criar uma nova entrada no diretório com um único `Id` . Deverá manter um mapeamento entre os utilizadores da sua aplicação e os Serviços de Comunicação gerados (por exemplo, armazenando-os na base de dados do seu servidor de aplicações).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Emitir fichas de acesso ao utilizador

Utilize o `issue_token` método para emitir um token de acesso para um utilizador dos Serviços de Comunicação. Se não fornecer o `user` parâmetro opcional, um novo utilizador será criado e devolvido com o token.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Os tokens de acesso ao utilizador são credenciais de curta duração que precisam de ser reemitidas de forma a evitar que os seus utilizadores experimentem perturbações de serviço. A `expires_on` propriedade de resposta indica a vida útil do símbolo.

## <a name="revoke-user-access-tokens"></a>Revogar fichas de acesso ao utilizador

Em alguns casos, poderá ser necessário revogar explicitamente as fichas de acesso ao utilizador, por exemplo, quando um utilizador altera a palavra-passe que utiliza para autenticar o seu serviço. Esta funcionalidade está disponível através da biblioteca de clientes da Azure Communication Services Administration.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Eliminar um utilizador

A eliminação de uma identidade revoga todos os tokens ativos e impede-o de emitir fichas subsequentes para as identidades. Também remove todo o conteúdo persistido associado ao utilizador.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Executar o código

A partir de um pedido de consola, navegue até ao diretório que contém o ficheiro *issue-token.py* e, em seguida, execute o seguinte `python` comando para executar a aplicação.

```console
python ./issue-token.py
```
