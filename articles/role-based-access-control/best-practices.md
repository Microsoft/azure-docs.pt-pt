---
title: Boas práticas para o Azure RBAC
description: Melhores práticas para a utilização do controlo de acesso baseado em funções Azure (Azure RBAC).
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
ms.openlocfilehash: 053e86f3493c7a11a3cbbaad0871e45345697878
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82735339"
---
# <a name="best-practices-for-azure-rbac"></a>Boas práticas para o Azure RBAC

Este artigo descreve algumas boas práticas para a utilização do controlo de acesso baseado em funções Azure (Azure RBAC). Estas boas práticas derivam da nossa experiência com o Azure RBAC e das experiências de clientes como você.

## <a name="only-grant-the-access-users-need"></a>Apenas conceder os utilizadores de acesso de necessidade

Com o RBAC do Azure, pode fazer a segregação de deveres na sua equipa e conceder aos utilizadores apenas a quantidade de acesso de que precisam para desempenhar as suas funções. Em vez de dar a todas as pessoas permissões sem restrições na sua subscrição do Azure ou recursos, pode permitir apenas determinadas ações num âmbito específico.

Quando planear a estratégia de controlo de acesso, é boa prática conceder aos utilizadores o privilégio menor de que precisam para trabalhar. O diagrama seguinte mostra um padrão sugerido para a utilização do RBAC Azure.

![Azure RBAC e menos privilégio](./media/best-practices/rbac-least-privilege.png)

Para obter informações sobre como adicionar atribuições de funções, consulte [adicionar ou remover atribuições de funções Azure utilizando o portal Azure](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Limitar o número de proprietários de assinaturas

Deverá ter um máximo de 3 proprietários de subscrições para reduzir o potencial de violação por parte de um proprietário comprometido. Esta recomendação pode ser monitorizada no Centro de Segurança Azure. Para outras recomendações de identidade e acesso no Centro de Segurança, consulte [recomendações de Segurança - um guia de referência](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Utilizar a Azure AD Gestão de Identidade Privilegiada

Para proteger contas privilegiadas de ciberataques maliciosos, pode utilizar a Azure Ative Directory Privileged Identity Management (PIM) para reduzir o tempo de exposição dos privilégios e aumentar a sua visibilidade para a sua utilização através de relatórios e alertas. A PIM ajuda a proteger as contas privilegiadas, proporcionando acesso privilegiado just-in-time aos recursos Azure AD e Azure. O acesso pode ser limitado pelo tempo após o qual os privilégios são revogados automaticamente. 

Para mais informações, veja [o que é Azure AD Gestão de Identidade Privilegiada?](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas Azure RBAC](troubleshooting.md)