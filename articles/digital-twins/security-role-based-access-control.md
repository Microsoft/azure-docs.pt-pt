---
title: Compreender o controlo de acesso baseado em papéis - Azure Digital Twins / Microsoft Docs
description: Conheça o controlo de acesso baseado em papéis e a gestão de permissões em Gémeas Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044916"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Controlo de acesso baseado em papéis em Gémeas Digitais Azure

A Azure Digital Twins permite um controlo preciso do acesso sobre dados, recursos e ações específicos no seu gráfico espacial. Fá-lo através do papel granular e da gestão da permissão chamado [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC). O RBAC é composto por _funções_ e atribuições de _papéis._ As funções identificam o nível de permissões. As atribuições de funções associam uma função a um utilizador ou dispositivo.

Utilizando o RBAC, a permissão pode ser concedida para:

- Um utilizador.
- Um dispositivo.
- Um diretor de serviço.
- Uma função definida pelo utilizador.
- Todos os utilizadores que pertencem a um domínio.
- Um inquilino.

O grau de acesso também pode ser afinado.

O RBAC é único na medida em que as permissões são herdadas pelo gráfico espacial.

## <a name="what-can-i-do-with-rbac"></a>O que posso fazer com o RBAC?

Um desenvolvedor pode usar RBAC para:

- Conceda a um utilizador a capacidade de gerir dispositivos para um edifício inteiro, ou apenas para um quarto ou piso específico.
- Conceda a um administrador o acesso global a todos os nós de gráficos espaciais para um gráfico inteiro, ou apenas para uma secção do gráfico.
- Conceda a um especialista de apoio que leia o acesso ao gráfico, exceto as chaves de acesso.
- Conceda a cada membro de um domínio que leia o acesso a todos os objetos gráficos.

## <a name="rbac-best-practices"></a>Boas práticas rBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Funções

### <a name="role-definitions"></a>Definições de função

Uma definição de papel é uma coleção de permissões e outros atributos que constituem um papel. Uma definição de função lista as operações permitidas, que incluem *CREATE,* *READ,* *UPDATE*, e *DELETE* que qualquer objeto com esse papel pode desempenhar. Especifica também a que tipos de objetos as permissões se aplicam.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Para recuperar as definições completas para as funções anteriores, consultar o sistema/funções API.
> Saiba mais lendo [criando e gerindo atribuições de papéis.](./security-create-manage-role-assignments.md#retrieve-all-roles)

### <a name="object-identifier-types"></a>Tipos de identificador de objetos

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Aprenda a conceder permissões ao seu diretor de serviço, lendo [criando e gerindo atribuições de papéis.](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)

Os seguintes artigos de documentação de referência descrevem:

- Como [consultar ou identificar o objeto para um utilizador](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Como [obter o ID do objeto para um diretor de serviço](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Como [recuperar o ID do objeto para um inquilino Azure AD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Atribuições de funções

Uma atribuição de funções de Azure Digital Twins associa um objeto, como um utilizador ou um inquilino DaD Azure, com um papel e um espaço. Permissões são concedidas a todos os objetos que pertencem a esse espaço. O espaço inclui todo o gráfico espacial por baixo.

Por exemplo, um utilizador recebe uma `DeviceInstaller` atribuição de funções com o papel para o nó raiz de um gráfico espacial, que representa um edifício. O utilizador pode então ler e atualizar dispositivos para esse nó e todos os outros espaços infantis do edifício.

Para conceder permissões a um destinatário, crie uma atribuição de funções. Para revogar permissões, remova a atribuição do papel.

>[!IMPORTANT]
> Saiba mais sobre atribuições de papéis lendo Criar e gerir atribuições de [papéis.](./security-create-manage-role-assignments.md)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a criação e gestão de atribuições de papéis da Azure Digital Twins, leia Criar e gerir atribuições de [papéis.](./security-create-manage-role-assignments.md)

- Leia mais sobre [rBAC para Azure](https://docs.microsoft.com/azure/role-based-access-control/).
