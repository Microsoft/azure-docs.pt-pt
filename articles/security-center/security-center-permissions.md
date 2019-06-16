---
title: Permissões no Centro de segurança do Azure | Documentos da Microsoft
description: Este artigo explica como o Centro de segurança do Azure utiliza o controlo de acesso baseado em funções para atribuir permissões a utilizadores e identifica as ações permitidas para cada função.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: barbkess
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: aa6c154e14fbf5291e2493f4f27b9d4501ba18f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60905624"
---
# <a name="permissions-in-azure-security-center"></a>Permissões no Centro de segurança do Azure

O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, apenas verá informações relacionadas com a um recurso quando for atribuída a função de proprietário, contribuinte ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

Além destas funções, há duas funções específicas do Centro de Segurança:

* **Leitor de segurança**: Um utilizador que pertence a esta função tem a ver direitos ao centro de segurança. O utilizador pode ver recomendações, alertas, uma política de segurança e Estados de segurança, mas não pode fazer alterações.
* **Administrador de segurança**: Um utilizador que pertence a esta função tem os mesmos direitos que o leitor de segurança e pode também atualizar a política de segurança e dispensar alertas e recomendações.

> [!NOTE]
> As funções de segurança, leitor de segurança e o administrador de segurança, tem acesso apenas no Centro de segurança. As funções de segurança não tem acesso a outras áreas de serviço do Azure como armazenamento, Web e móveis ou de Internet das coisas.
>
>

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela seguinte mostra as funções e permitido ações no Centro de segurança. Um X indica que a ação tem permissões para essa função.

| Função | Editar política de segurança | Aplicar recomendações de segurança de um recurso | Dispensar alertas e recomendações | Ver alertas e recomendações |
|:--- |:---:|:---:|:---:|:---:|
| Proprietário da subscrição | X | X | X | X |
| Contribuinte da subscrição | -- | X | X | X |
| Proprietário do grupo de recursos | -- | X | -- | X |
| Contribuinte do grupo de recursos | -- | X | -- | X |
| Leitor | -- | -- | -- | X |
| Administrador de Segurança | X | -- | X | X |
| Leitor de segurança | -- | -- | -- | X |

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribua a função de leitor para utilizadores que apenas precisam de ver informações sobre o estado de funcionamento de segurança de um recurso, mas não tomar medidas, como aplicar recomendações ou editar políticas.
>
>

## <a name="next-steps"></a>Passos Seguintes
Este artigo explicou como o Centro de segurança utiliza o RBAC para atribuir permissões a utilizadores e identificou as ações permitidas para cada função. Agora que está familiarizado com as atribuições de função necessárias para monitorizar o estado de segurança da sua subscrição, editar as políticas de segurança e aplicar recomendações, saiba como:

- [Definir políticas de segurança no Centro de segurança](tutorial-security-policy.md)
- [Gerir recomendações de segurança no Centro de segurança](security-center-recommendations.md)
- [Monitorizar o estado de funcionamento de segurança dos seus recursos do Azure](security-center-monitoring.md)
- [Gerir e responder a alertas de segurança no Centro de Segurança](security-center-managing-and-responding-alerts.md)
- [Monitorizar soluções de segurança de parceiros](security-center-partner-solutions.md)
