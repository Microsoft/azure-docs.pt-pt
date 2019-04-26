---
title: Compreender o controlo de acesso baseado em funções de duplos Digital do Azure | Documentos da Microsoft
description: Aprenda a autenticação no Digital duplos com controlo de acesso baseado em funções.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyrana
ms.openlocfilehash: bfc73a71a0ccda5c135e6a740d6f63bd37522a9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534157"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Controlo de acesso baseado em funções no duplos Digital do Azure

Duplos Digital do Azure permite que o controlo de acesso exatos para as ações no seu gráfico espacial, recursos e dados específicos. Ele faz isso por meio da função granular e gestão de permissões chamado controlo de acesso baseado em funções (RBAC). Consiste em RBAC _funções_ e _atribuições de funções_. Funções de identificam o nível de permissões. Atribuições de funções de associar uma função de um utilizador ou dispositivo.

Utilizando o RBAC, pode ser concedida permissão para:

- Um utilizador.
- Um dispositivo.
- Um principal de serviço.
- Uma função definida pelo utilizador.
- Todos os utilizadores que pertencem a um domínio.
- Um inquilino.

O grau de acesso também pode ser otimizado.

RBAC é o único que permissões são herdadas para baixo do gráfico espacial.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Um desenvolvedor poderá utilizar o RBAC para:

- Conceda a capacidade para gerir os dispositivos para uma compilação inteira ou apenas para uma sala específica ou o piso de um utilizador.
- Concessão de um administrador global acesso a todos os nós de gráfico geográficos para um gráfico inteiro ou apenas para uma secção do gráfico.
- Conceda um acesso de leitura de especialista em suporte ao gráfico, exceto para as chaves de acesso.
- Conceda a todos os membros de um acesso de leitura de domínio para todos os objetos de gráfico.

## <a name="rbac-best-practices"></a>Melhores práticas RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Funções

### <a name="role-definitions"></a>Definições de funções

Uma definição de função é um conjunto de permissões e outros atributos que constituem uma função. Uma definição de função apresenta uma lista as operações permitidas, que incluem *CREATE*, *ler*, *ATUALIZAÇÃO*, e *eliminar* que qualquer objeto com que Pode executar a função. Também especifica a que se aplicam as permissões de tipos de objeto para.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Para obter as definições de completas para as funções anteriores, consulte as API de sistema/funções.
> Saber mais, leia [criar e gerir as atribuições de função](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Tipos de identificador de objeto

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Saiba como pode conceder permissões ao principal de serviço lendo [criar e gerir as atribuições de função](./security-create-manage-role-assignments.md#grant).

Os seguintes artigos de documentação de referência descrevem:

- Como [consulta ou o ID de objeto para um utilizador](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Como [obter o ID de objeto para um principal de serviço](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Como [obter o ID de objeto para um inquilino do Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Atribuições de funções

Uma atribuição de função duplos Digital do Azure associa um objeto, como um utilizador ou um inquilino do Azure AD, uma função e um espaço. Permissões são concedidas a todos os objetos que pertencem a esse espaço. O espaço inclui todo o gráfico geográfico abaixo dele.

Por exemplo, um utilizador recebe uma atribuição de função com a função `DeviceInstaller` para o nó de raiz de um gráfico espacial, que representa um edifício. O utilizador pode, em seguida, ler e atualizar os dispositivos para esse nó e todos os outros espaços de subordinados no prédio.

Para conceder permissões a um destinatário, crie uma atribuição de função. Para revogar as permissões, remova a atribuição de função.

>[!IMPORTANT]
> Saiba mais sobre as atribuições de funções lendo [criar e gerir as atribuições de função](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre criar e gerir as atribuições de função de duplos Digital do Azure, leia [criar e gerir atribuições de funções](./security-create-manage-role-assignments.md).
