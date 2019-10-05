---
title: Entender o controle de acesso baseado em função – gêmeos digital do Azure | Microsoft Docs
description: Saiba mais sobre o controle de acesso baseado em função e o gerenciamento de permissões no Azure digital gêmeos.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 1d4243457f51f11db5bc9681b29d27293fbc6250
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949042"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Controle de acesso baseado em função no Azure digital gêmeos

O Azure digital gêmeos permite o controle de acesso preciso a dados, recursos e ações específicos em seu grafo espacial. Ele faz isso por meio de função granular e gerenciamento de permissões chamado de RBAC (controle de acesso baseado em função). O RBAC consiste em _funções_ e _atribuições de função_. As funções identificam o nível de permissões. As atribuições de função associam uma função a um usuário ou dispositivo.

Usando o RBAC, a permissão pode ser concedida a:

- Um usuário.
- Um dispositivo.
- Uma entidade de serviço.
- Uma função definida pelo usuário.
- Todos os usuários que pertencem a um domínio.
- Um locatário.

O grau de acesso também pode ser ajustado.

O RBAC é exclusivo, pois as permissões são herdadas para baixo no grafo espacial.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Um desenvolvedor pode usar o RBAC para:

- Conceder a um usuário a capacidade de gerenciar dispositivos para um edifício inteiro, ou apenas para uma sala ou um andar específico.
- Conceda a um administrador acesso global a todos os nós de grafo espaciais para um grafo inteiro ou apenas para uma seção do grafo.
- Conceda a um especialista de suporte acesso de leitura ao grafo, exceto as chaves de acesso.
- Conceder a cada membro de um domínio acesso de leitura a todos os objetos de grafo.

## <a name="rbac-best-practices"></a>Práticas recomendadas do RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Funções

### <a name="role-definitions"></a>Definições de função

Uma definição de função é uma coleção de permissões e outros atributos que constituem uma função. Uma definição de função lista as operações permitidas, que incluem *criar*, *ler*, *Atualizar*e *excluir* que qualquer objeto com essa função possa ser executado. Ele também especifica a quais tipos de objeto as permissões se aplicam.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Para recuperar as definições completas das funções anteriores, consulte a API de sistema/funções.
> Saiba mais lendo [criando e gerenciando atribuições de função](./security-create-manage-role-assignments.md#retrieve-all-roles).

### <a name="object-identifier-types"></a>Tipos de identificador de objeto

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Saiba como conceder permissões para sua entidade de serviço lendo [criando e gerenciando atribuições de função](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

Os artigos de documentação de referência a seguir descrevem:

- Como [consultar ou a ID de objeto de um usuário](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Como [obter a ID de objeto para uma entidade de serviço](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Como [recuperar a ID de objeto para um locatário do Azure ad](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Atribuições de funções

Uma atribuição de função gêmeos digital do Azure associa um objeto, como um usuário ou um locatário do Azure AD, com uma função e um espaço. As permissões são concedidas a todos os objetos que pertencem a esse espaço. O espaço inclui o gráfico espacial inteiro abaixo dele.

Por exemplo, um usuário recebe uma atribuição de função com a função `DeviceInstaller` para o nó raiz de um grafo espacial, que representa um edifício. O usuário pode ler e atualizar dispositivos para esse nó e todos os outros espaços filho na construção.

Para conceder permissões a um destinatário, crie uma atribuição de função. Para revogar permissões, remova a atribuição de função.

>[!IMPORTANT]
> Saiba mais sobre as atribuições de função lendo [criando e gerenciando atribuições de função](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como criar e gerenciar atribuições de função do gêmeos digital do Azure, leia [criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).