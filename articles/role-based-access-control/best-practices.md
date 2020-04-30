---
title: Boas práticas para O RBAC Azure
description: Boas práticas para utilizar o controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726777"
---
# <a name="best-practices-for-azure-rbac"></a>Boas práticas para O RBAC Azure

Este artigo descreve algumas boas práticas para utilizar o controlo de acesso baseado em funções azure (Azure RBAC). Estas boas práticas derivam da nossa experiência com o Azure RBAC e as experiências de clientes como você.

## <a name="only-grant-the-access-users-need"></a>Apenas conceder os utilizadores de acesso precisam

Utilizando o Azure RBAC, pode segregar tarefas dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores de que necessitam para desempenhar em seu trabalho. Em vez de dar a todas as pessoas permissões sem restrições na sua subscrição do Azure ou recursos, pode permitir apenas determinadas ações num âmbito específico.

Quando planear a estratégia de controlo de acesso, é boa prática conceder aos utilizadores o privilégio menor de que precisam para trabalhar. O diagrama seguinte mostra um padrão sugerido para utilizar o RBAC.

![RBAC e menor privilégio](./media/best-practices/rbac-least-privilege.png)

Para obter informações sobre como adicionar atribuições de papéis, consulte [Adicionar ou remover atribuições](role-assignments-portal.md)de funções .

## <a name="limit-the-number-of-subscription-owners"></a>Limitar o número de proprietários de assinaturas

Deverá ter um máximo de 3 proprietários de subscrições para reduzir o potencial de violação por parte de um proprietário comprometido. Esta recomendação pode ser monitorizada no Centro de Segurança Azure. Para outras recomendações de identidade e acesso no Centro de Segurança, consulte [recomendações](../security-center/recommendations-reference.md)de segurança - um guia de referência .

## <a name="use-azure-ad-privileged-identity-management"></a>Utilizar a Gestão de Identidade Privilegiada da Azure AD

Para proteger contas privilegiadas de ciberataques maliciosos, pode utilizar a Azure Ative Directory Privileged Identity Management (PIM) para diminuir o tempo de exposição dos privilégios e aumentar a sua visibilidade para o seu uso através de relatórios e alertas. A PIM ajuda a proteger contas privilegiadas, proporcionando acesso privilegiado aos recursos Azure AD e Azure. O acesso pode ser limitado pelo tempo após o qual os privilégios são revogados automaticamente. 

Para mais informações, consulte o que é a [Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas Azure RBAC](troubleshooting.md)