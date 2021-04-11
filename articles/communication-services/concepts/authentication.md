---
title: Autenticar para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Conheça as várias formas de uma aplicação ou serviço poder autenticar os Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110870"
---
# <a name="authenticate-to-azure-communication-services"></a>Autenticar para serviços de comunicação Azure

Todas as interações dos clientes com os Serviços de Comunicação Azure têm de ser autenticadas. Numa arquitetura típica, consulte [a arquitetura do cliente e do servidor,](./client-and-server-architecture.md)as chaves de *acesso* ou *identidades geridas* são utilizadas para a autenticação.

Outro tipo de autenticação utiliza *fichas de acesso ao utilizador* para autenticar contra serviços que requerem a participação do utilizador. Por exemplo, o serviço de chat ou chamada utiliza *fichas de acesso ao utilizador* para permitir que os utilizadores sejam adicionados numa linha e tenham conversas entre si.

## <a name="authentication-options"></a>Opções de autenticação

A tabela a seguir mostra os SDKs dos Serviços de Comunicação Azure e as suas opções de autenticação:

| SDK    | Opção de autenticação                               |
| ----------------- | ----------------------------------------------------|
| Identidade          | Chave de acesso ou identidade gerida                      |
| SMS               | Chave de acesso ou identidade gerida                      |
| Números de telefone     | Chave de acesso ou identidade gerida                      |
| Chamando           | Token de acesso ao utilizador                                   |
| Chat              | Token de acesso ao utilizador                                   |

Cada opção de autorização é brevemente descrita abaixo:

### <a name="access-key"></a>Chave de Acesso

A autenticação da chave de acesso é adequada para aplicações de serviço que são executando num ambiente de serviço de confiança. A sua chave de acesso pode ser encontrada no portal Azure Communication Services. A aplicação de serviço usa-a como credencial para inicializar os SDKs correspondentes. Veja um exemplo de como é usado no [SDK de identidade.](../quickstarts/access-tokens.md) 

Uma vez que a chave de acesso faz parte da cadeia de ligação do seu recurso, a autenticação com uma cadeia de ligação equivale à autenticação com uma chave de acesso.

Se desejar ligar manualmente para as APIs da ACS utilizando uma chave de acesso, terá de assinar o pedido. A assinatura do pedido é explicada, em detalhe, dentro de um [tutorial.](../tutorials/hmac-header-tutorial.md)

### <a name="managed-identity"></a>Identidade Gerida

Identidades geridas, proporciona segurança superior e facilidade de utilização sobre outras opções de autorização. Por exemplo, ao utilizar o Azure AD, evita ter de armazenar a chave de acesso à conta dentro do seu código, como faz com a autorização da Chave de Acesso. Embora possa continuar a utilizar a autorização da Chave de Acesso com aplicações de serviços de comunicação, a Microsoft recomenda que se mude para a Azure AD sempre que possível. 

Para criar uma identidade gerida, [crie um pedido registado a partir do Azure CLI](../quickstarts/managed-identity-from-cli.md). Em seguida, o ponto final e as credenciais podem ser usados para autenticar os SDKs. Veja exemplos de como a [identidade gerida](../quickstarts/managed-identity.md) é usada.

### <a name="user-access-tokens"></a>Tokens de acesso ao utilizador

Os tokens de acesso ao utilizador são gerados usando o SDK de identidade e estão associados com utilizadores criados no SDK de identidade. Veja um exemplo de como [criar utilizadores e gerar fichas.](../quickstarts/access-tokens.md) Em seguida, os tokens de acesso ao utilizador são usados para autenticar os participantes adicionados a conversas no Chat ou Call SDK. Para mais informações, consulte [adicionar chat à sua aplicação.](../quickstarts/chat/get-started.md) A autenticação de token de acesso ao utilizador é diferente em comparação com a chave de acesso e a autenticação de identidade gerida, na medida em que é usada para autenticar um utilizador em vez de um recurso Azure seguro.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar e gerir recursos](../quickstarts/create-communication-resource.md) 
>  dos Serviços de Comunicação [Criar um Azure Ative Directory gerido aplicação de identidade a partir do Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Criar fichas de acesso ao utilizador](../quickstarts/access-tokens.md)

Para obter mais informações, veja os seguintes artigos:
- [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
