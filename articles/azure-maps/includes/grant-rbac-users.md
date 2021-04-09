---
title: Conceder acesso baseado em funções para utilizadores
titleSuffix: Azure Maps
description: Utilize o controlo de acesso baseado em funções para conceder autorização aos utilizadores para o Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101102823"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Conceder acesso baseado em funções para utilizadores ao Azure Maps

Você concede *ao Azure um controlo de acesso baseado em funções (Azure RBAC)* atribuindo um grupo AD Azure ou princípios de segurança a uma ou mais definições de papel do Azure Maps. Para ver as definições de função Azure que estão disponíveis para O Azure Maps, vá ao **Controlo de Acesso (IAM)**. Selecione **Roles**, e, em seguida, procure por papéis que comecem com *Azure Maps*.

* Para gerir de forma eficiente uma grande quantidade de acesso dos utilizadores ao Azure Maps, consulte [os Grupos AD Azure.](../../active-directory/fundamentals/active-directory-manage-groups.md)
* Para que os utilizadores possam autenticar a aplicação, os utilizadores devem ser criados em Azure AD. Ver [Adicionar ou Eliminar utilizadores usando Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Leia mais sobre [a Azure AD](../../active-directory/fundamentals/index.yml) para gerir eficazmente um diretório para utilizadores.

1. Aceda à sua **Conta Azure Maps.** Selecione a atribuição de função **do controlo de acesso (IAM).**  >  

    ![Conceder acesso usando Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. No separador **Role assignments,** em **Role**, selecione uma definição de função incorporada no Azure Maps, como O Leitor **de Dados Azure Maps** ou **Azure Maps Data Contributor**. No **acesso de Atribuição a**, selecione **Azure AD utilizador, grupo ou principal de serviço**. Selecione o principal pelo nome. Em seguida, selecione **Guardar**.

   * Consulte os detalhes sobre [as funções de Atribuição Azure](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> As definições de funções incorporadas Azure Maps proporcionam um acesso de autorização muito grande a muitas APIs DE REST Azure Maps. Para restringir ao mínimo as APIs para os utilizadores, consulte [criar uma definição de função personalizada e atribuir os utilizadores](../../role-based-access-control/custom-roles.md) à definição de função personalizada. Isto permitirá aos utilizadores ter o menor privilégio necessário para a aplicação.