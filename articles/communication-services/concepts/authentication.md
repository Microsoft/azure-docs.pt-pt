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
ms.openlocfilehash: b3cd0643a74ccadb8390ce906eb391420de15a29
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490794"
---
# <a name="authenticate-to-azure-communication-services"></a>Autenticar para serviços de comunicação Azure

Todas as interações dos clientes com os Serviços de Comunicação Azure têm de ser autenticadas. Numa arquitetura típica, consulte [a arquitetura do cliente e do servidor,](./client-and-server-architecture.md)as *teclas de acesso* ou identidade *gerida* são utilizadas no serviço de acesso ao utilizador confiável para criar utilizadores e emitir fichas. E *o token de acesso ao utilizador* emitido pelo serviço de acesso ao utilizador confiável é utilizado para aplicações de clientes para aceder a outros serviços de comunicação, por exemplo, serviço de chat ou chamada.

O serviço de SMS dos Serviços de Comunicação Azure também aceita chaves de *acesso* ou *identidade gerida* para autenticação. Isto normalmente acontece numa aplicação de serviço em execução num ambiente de serviço de confiança.

Cada opção de autorização é brevemente descrita abaixo:

- **Acesso Autenticação chave** para operações de SMS e Identidade. A autenticação da Chave de Acesso é adequada para aplicações de serviço que são executando num ambiente de serviço de confiança. A chave de acesso pode ser encontrada no portal dos Serviços de Comunicação Azure. Para autenticar com uma chave de acesso, uma aplicação de serviço utiliza a chave de acesso como credencial para inicializar as bibliotecas correspondentes de SMS ou clientes de identidade, ver [Criar e gerir fichas de acesso.](../quickstarts/access-tokens.md) Uma vez que a chave de acesso faz parte da cadeia de ligação do seu recurso, consulte [criar e gerir os recursos dos Serviços de Comunicação,](../quickstarts/create-communication-resource.md)a autenticação com fio de ligação é equivalente à autenticação com chave de acesso.
- **Autenticação de Identidade Gerida** para operações de SMS e Identidade. Identidade Gerida, ver [Identidade Gerida,](../quickstarts/managed-identity.md)é adequado para aplicações de serviço em execução em um ambiente de serviço de confiança. Para autenticar com uma identidade gerida, uma aplicação de serviço cria uma credencial com o ID e um segredo da identidade gerida, em seguida, inicializar as bibliotecas correspondentes de SMS ou identidade cliente, ver [Criar e gerir fichas de acesso.](../quickstarts/access-tokens.md)
- **Autenticação do Token de acesso ao utilizador** para chat e chamada. As fichas de acesso ao utilizador permitem que as aplicações do seu cliente autentem contra o Azure Communication Chat e os Serviços de Chamada. Estes tokens são gerados num "serviço de acesso ao utilizador confiável" que cria. São então fornecidos a dispositivos clientes que usam o símbolo para inicializar as bibliotecas de clientes Chat e Call. Para obter mais informações, consulte [Adicionar Chat à sua App,](../quickstarts/chat/get-started.md) por exemplo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar e gerir recursos](../quickstarts/create-communication-resource.md) 
>  dos Serviços de Comunicação [Criar um Azure Ative Directory gerido aplicação de identidade a partir do Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  [Criação de fichas de acesso ao utilizador](../quickstarts/access-tokens.md)

Para obter mais informações, veja os seguintes artigos:
- [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
