---
title: Permissões no Centro de Segurança Azure Microsoft Docs
description: Este artigo explica como o Azure Security Center utiliza o controlo de acesso baseado em papéis para atribuir permissões aos utilizadores e identifica as ações permitidas para cada função.
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
ms.openlocfilehash: 6e61571400930d4a781d6d67647bd662a7f2d350
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106224"
---
# <a name="permissions-in-azure-security-center"></a>Permissions in Azure Security Center (Permissões no Centro de Segurança do Azure)

O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

O Security Center avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de Segurança, só se vê informações relacionadas com um recurso quando lhe é atribuído o papel de Proprietário, Colaborador ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso.

Além destas funções, há duas funções específicas do Centro de Segurança:

* **Leitor de Segurança**: Um utilizador que pertence a esta função tem direitos de visualização para o Centro de Segurança. O utilizador pode ver recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações.
* **Administrador de Segurança**: Um utilizador que pertence a esta função tem os mesmos direitos que o Leitor de Segurança e também pode atualizar a política de segurança e rejeitar alertas e recomendações.

> [!NOTE]
> As funções de segurança, Security Reader e Security Admin, têm acesso apenas no Centro de Segurança. As funções de segurança não têm acesso a outras áreas de serviço do Azure, tais como Armazenamento, Web & Mobile ou Internet of Things.
>
>

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela seguinte exibe funções e permitiu ações no Centro de Segurança.

| Função | Editar política de segurança | Aplicar recomendações de segurança para um recurso</br> (incluindo com 'Quick Fix!') | Descartar alertas e recomendações | Ver alertas e recomendações |
|:--- |:---:|:---:|:---:|:---:|
| Proprietário de assinatura | ✔ | ✔ | ✔ | ✔ |
| Colaborador de Assinatura | -- | ✔ | ✔ | ✔ |
| Proprietário do Grupo de Recursos | -- | ✔ | -- | ✔ |
| Colaborador do Grupo de Recursos | -- | ✔ | -- | ✔ |
| Leitor | -- | -- | -- | ✔ |
| Administrador de Segurança | ✔ | -- | ✔ | ✔ |
| Leitor de Segurança | -- | -- | -- | ✔ |

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribuir o papel do Leitor aos utilizadores que apenas precisam de ver informações sobre a saúde de segurança de um recurso, mas não tomar medidas, tais como a aplicação de recomendações ou políticas de edição.
>
>

## <a name="next-steps"></a>Passos seguintes
Este artigo explicou como o Security Center usa o RBAC para atribuir permissões aos utilizadores e identificou as ações permitidas para cada função. Agora que está familiarizado com as atribuições de funções necessárias para monitorizar o estado de segurança da sua subscrição, editar políticas de segurança e aplicar recomendações, aprenda a:

- [Definir políticas de segurança no Centro de Segurança](tutorial-security-policy.md)
- [Gerir recomendações de segurança no Centro de Segurança](security-center-recommendations.md)
- [Monitorize a saúde de segurança dos seus recursos Azure](security-center-monitoring.md)
- [Gerir e responder a alertas de segurança no Centro de Segurança](security-center-managing-and-responding-alerts.md)
- [Monitorizar as soluções de segurança dos parceiros](security-center-partner-solutions.md)
