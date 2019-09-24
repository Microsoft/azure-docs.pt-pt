---
title: Permissões na central de segurança do Azure | Microsoft Docs
description: Este artigo explica como a central de segurança do Azure usa o controle de acesso baseado em função para atribuir permissões a usuários e identifica as ações permitidas para cada função.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: f52d518d2ed1dfb62eed72cf9c0b839a37b7f856
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201656"
---
# <a name="permissions-in-azure-security-center"></a>Permissões na central de segurança do Azure

O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, apenas verá informações relacionadas com a um recurso quando for atribuída a função de proprietário, contribuinte ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

Além destas funções, há duas funções específicas do Centro de Segurança:

* **Leitor de segurança**: Um usuário que pertence a essa função tem direitos de exibição para a central de segurança. O usuário pode exibir recomendações, alertas, uma política de segurança e Estados de segurança, mas não pode fazer alterações.
* **Administrador de segurança**: Um usuário que pertence a essa função tem os mesmos direitos que o leitor de segurança e também pode atualizar a política de segurança e ignorar alertas e recomendações.

> [!NOTE]
> As funções de segurança, o leitor de segurança e o administrador de segurança têm acesso apenas na central de segurança. As funções de segurança não têm acesso a outras áreas de serviço do Azure, como armazenamento, Web & móvel ou Internet das Coisas.
>
>

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela a seguir exibe funções e ações permitidas na central de segurança. Um X indica que a ação é permitida para essa função.

| Role | Editar política de segurança | Aplicar recomendações de segurança para um recurso | Ignorar alertas e recomendações | Exibir alertas e recomendações |
|:--- |:---:|:---:|:---:|:---:|
| Proprietário da assinatura | X | X | X | X |
| Colaborador da assinatura | -- | X | X | X |
| Proprietário do grupo de recursos | -- | X | -- | X |
| Colaborador do grupo de recursos | -- | X | -- | X |
| Leitor | -- | -- | -- | X |
| Administrador de Segurança | X | -- | X | X |
| Leitor de Segurança | -- | -- | -- | X |

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribua a função leitor a usuários que precisam apenas exibir informações sobre a integridade de segurança de um recurso, mas não executar ações, como aplicar recomendações ou editar políticas.
>
>

## <a name="next-steps"></a>Passos seguintes
Este artigo explicou como a central de segurança usa o RBAC para atribuir permissões aos usuários e identificar as ações permitidas para cada função. Agora que você está familiarizado com as atribuições de função necessárias para monitorar o estado de segurança de sua assinatura, editar políticas de segurança e aplicar recomendações, saiba como:

- [Definir políticas de segurança na central de segurança](tutorial-security-policy.md)
- [Gerenciar recomendações de segurança na central de segurança](security-center-recommendations.md)
- [Monitorar a integridade da segurança de seus recursos do Azure](security-center-monitoring.md)
- [Gerir e responder a alertas de segurança no Centro de Segurança](security-center-managing-and-responding-alerts.md)
- [Monitorar soluções de segurança de parceiros](security-center-partner-solutions.md)
