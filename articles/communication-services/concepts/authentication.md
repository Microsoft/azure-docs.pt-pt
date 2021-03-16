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
ms.openlocfilehash: 83976ed9d6f80b6c785cb84e74a0755472f9579f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561809"
---
# <a name="authenticate-to-azure-communication-services"></a>Autenticar para serviços de comunicação Azure

Todas as interações dos clientes com os Serviços de Comunicação Azure têm de ser autenticadas. Numa arquitetura típica, consulte [a arquitetura do cliente e do servidor,](./client-and-server-architecture.md)as chaves de *acesso* ou *identidades geridas* são utilizadas para a autenticação.

Outro tipo de autenticação utiliza *fichas de acesso ao utilizador* para autenticar contra serviços que requerem a participação do utilizador. Por exemplo, o serviço de chat ou chamada utiliza *fichas de acesso ao utilizador* para permitir que os utilizadores sejam adicionados numa linha e tenham conversas entre si.

## <a name="authentication-options"></a>Opções de autenticação:

A tabela a seguir mostra as bibliotecas de clientes dos Serviços de Comunicação Azure e as suas opções de autenticação:

| Biblioteca do Cliente    | Opção de autenticação                               |
| ----------------- | ----------------------------------------------------|
| Identidade          | Chave de acesso ou identidade gerida                      |
| SMS               | Chave de acesso ou identidade gerida                      |
| Números de telefone     | Chave de acesso ou identidade gerida                      |
| Chamando           | Token de acesso ao utilizador                                   |
| Chat              | Token de acesso ao utilizador                                   |

Cada opção de autorização é brevemente descrita abaixo:

- **A** autenticação da Chave de Acesso é adequada para aplicações de serviço que são executando num ambiente de serviço de confiança. A chave de acesso pode ser encontrada no portal Azure Communication Services e a aplicação de serviço usa-a como credencial para inicializar as bibliotecas de clientes correspondentes. Veja um exemplo com a forma como é usado na biblioteca do [cliente identidade.](../quickstarts/access-tokens.md) Uma vez que a chave de acesso faz parte da cadeia de ligação do seu recurso, a autenticação com uma cadeia de ligação equivale à autenticação com uma chave de acesso.

- **A** autenticação de identidade gerida proporciona uma segurança superior e facilidade de utilização em relação a outras opções de autorização. Por exemplo, ao utilizar o Azure AD, evita ter de armazenar a chave de acesso à conta com o seu código, como faz com a autorização da Chave de Acesso. Embora possa continuar a utilizar a autorização da Chave de Acesso com aplicações de serviços de comunicação, a Microsoft recomenda que se mude para a Azure AD sempre que possível. Para criar uma identidade gerida, [crie um pedido registado a partir do Azure CLI](../quickstarts/managed-identity-from-cli.md). Em seguida, o ponto final e as credenciais podem ser usados para autenticar as bibliotecas do cliente. Veja exemplos de como a [identidade gerida](../quickstarts/managed-identity.md) é usada.

- **Os Tokens de Acesso ao Utilizador** são gerados através da biblioteca do cliente Identidade e estão associados aos utilizadores criados na biblioteca do cliente Identidade. Veja um exemplo de como [criar utilizadores e gerar fichas.](../quickstarts/access-tokens.md) Em seguida, os tokens de acesso ao utilizador são usados para autenticar os participantes adicionados a conversas no Chat ou Call SDK. Para mais informações, consulte [adicionar chat à sua aplicação.](../quickstarts/chat/get-started.md) A autenticação de token de acesso ao utilizador é diferente em comparação com a chave de acesso e a autenticação de identidade gerida, na medida em que é usada para autenticar um utilizador em vez de um recurso Azure seguro.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar e gerir recursos](../quickstarts/create-communication-resource.md) 
>  dos Serviços de Comunicação [Criar um Azure Ative Directory gerido aplicação de identidade a partir do Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Criar fichas de acesso ao utilizador](../quickstarts/access-tokens.md)

Para obter mais informações, veja os seguintes artigos:
- [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
