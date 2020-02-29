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
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921322"
---
# <a name="permissions-in-azure-security-center"></a>Permissões no Centro de Segurança Azure

O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, apenas verá informações relacionadas com a um recurso quando for atribuída a função de proprietário, contribuinte ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

Além destas funções, há duas funções específicas do Centro de Segurança:

* **Leitor de Segurança**: Um utilizador que pertence a esta função tem direitos de visualização para o Centro de Segurança. O utilizador pode ver recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações.
* **Administrador de Segurança**: Um utilizador que pertença a esta função tem os mesmos direitos que o Leitor de Segurança e também pode atualizar a política de segurança e rejeitar alertas e recomendações.

> [!NOTE]
> As funções de segurança, Leitor de Segurança e Administrador de Segurança, têm acesso apenas no Centro de Segurança. As funções de segurança não têm acesso a outras áreas de serviço do Azure, tais como Armazenamento, Web & Mobile ou Internet das Coisas.
>
>

## <a name="roles-and-allowed-actions"></a>Papéis e ações permitidas

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
