---
title: Permissões no Azure Sentinel | Microsoft Docs
description: Este artigo explica como o Azure Sentinel usa o controle de acesso baseado em função para atribuir permissões a usuários e identifica as ações permitidas para cada função.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 02d87ed5f26d36b7cd438b0d818c7bdcce43520d
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464122"
---
# <a name="permissions-in-azure-sentinel"></a>Permissões no Azure Sentinel

O Azure Sentinel usa o [RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md)para fornecer [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

Usando o RBAC, você pode usar e criar funções dentro de sua equipe de operações de segurança para conceder acesso apropriado ao Azure Sentinel. Com base nas funções, você tem controle refinado sobre o que os usuários com acesso ao Azure Sentinel podem ver. Você pode atribuir funções de RBAC no espaço de trabalho do Azure Sentinel diretamente ou a uma assinatura ou a um grupo de recursos ao qual o espaço de trabalho pertence.

Há três funções internas de sentinela do Azure específicas.  
**Todas as funções internas do Azure Sentinel concedem acesso de leitura aos dados em seu espaço de trabalho do Azure Sentinel.**
- **Leitor do Azure Sentinel**: para obter mais informações, consulte [funções internas](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- **Respondente do Azure Sentinel**: para obter mais informações, consulte [funções internas](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- **Colaborador do Azure Sentinel**: para obter mais informações, consulte [funções internas](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Além das funções RBAC dedicadas do Azure Sentinel, há funções do Azure e Log Analytics RBAC que podem conceder um conjunto mais amplo de permissões que incluem acesso ao seu espaço de trabalho do Azure Sentinel e a outros recursos:

- **Funções do Azure:** [proprietário](../role-based-access-control/built-in-roles.md#owner), [colaborador](../role-based-access-control/built-in-roles.md#contributor)e [leitor](../role-based-access-control/built-in-roles.md#reader). As funções do Azure concedem acesso em todos os seus recursos do Azure, incluindo espaços de trabalho Log Analytics e recursos do Azure Sentinel.

-   **Log Analytics funções:** [colaborador de log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics funções concedem acesso em todos os seus espaços de trabalho do Log Analytics. 

> [!NOTE]
> Log Analytics funções também concedem acesso de leitura em todos os recursos do Azure, mas só atribuirão permissões de gravação a recursos de Log Analytics.


Por exemplo, um usuário atribuído com as funções **leitor do Azure Sentinel Reader** e **colaborador do Azure** (não o **colaborador do Azure Sentinel**) poderá editar dados no Azure Sentinel, embora eles tenham apenas permissões de **leitor de sentinela** . Portanto, se você quiser conceder permissões a apenas no Azure Sentinel, deverá remover cuidadosamente as permissões anteriores do usuário, certificando-se de não interromper nenhuma função de permissão necessária para outro recurso.

> [!NOTE]
>- O Azure Sentinel usa guias estratégicos para resposta automatizada contra ameaças. Os guias estratégicos aproveitam os aplicativos lógicos do Azure e são um recurso separado do Azure. Talvez você queira atribuir membros específicos de sua equipe de operações de segurança com a opção de usar aplicativos lógicos para operações de orquestração de segurança, automação e resposta (disparar). Você pode usar a função [colaborador do aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor) ou a função [operador do aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-operator) para atribuir permissão explícita para usar guias estratégicos.
>- Para adicionar conectores de dados, as funções necessárias para cada conector são por tipo de conector e são listadas na página do conector relevante. Além disso, para conectar qualquer fonte de dados, você deve ter permissão de gravação no espaço de trabalho do Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela a seguir exibe funções e ações permitidas no Azure Sentinel. Um X indica que a ação é permitida para essa função.

| Função | Criar e executar guias estratégicos| Criar e editar painéis, regras analíticas e outros recursos do Azure Sentinel | Gerenciar incidentes (ignorar, atribuir, etc.) | Exibir dados, incidentes, painéis e outros recursos do Azure Sentinel |
|--- |---|---|---|---|
| Leitor do Azure Sentinel | -- | -- | -- | X |
| Respondente do Azure Sentinel|--|--| X | X |
| Colaborador do Azure Sentinel | -- | X | X | X |
| Colaborador do Azure Sentinel + colaborador do aplicativo lógico | X | X | X | X |


> [!NOTE]
> - Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribua a função de colaborador do Azure Sentinel somente a usuários que precisam criar regras ou painéis.
> - Recomendamos que você defina permissões para o Azure Sentinel no escopo do grupo de recursos, para que o usuário possa ter acesso a todos os espaços de trabalho do Azure Sentinel no mesmo grupo de recursos.
>
## <a name="building-custom-rbac-roles"></a>Criando funções personalizadas de RBAC

Além de, ou em vez de, usando funções RBAC internas, você pode criar funções RBAC personalizadas para o Azure Sentinel. As funções personalizadas do RBAC para o Azure Sentinel são criadas da mesma maneira que você cria outras funções [personalizadas do Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) , com base em permissões específicas para recursos do Azure Sentinel.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>RBAC avançado nos dados que você armazena no Azure Sentinel
  
Você pode usar o Log Analytics controle de acesso baseado em função avançado nos dados em seu espaço de trabalho do Azure Sentinel. Isso inclui o controle de acesso baseado em função por tipo de dados e controle de acesso baseado em função centrado em recursos. Para obter mais informações sobre Log Analytics funções, consulte [gerenciar dados de log e espaços de trabalho no Azure monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Passos Seguintes
Neste documento, você aprendeu a trabalhar com funções para usuários do Azure Sentinel e o que cada função permite que os usuários façam.

* [Blog do Azure Sentinel](https://aka.ms/azuresentinelblog). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
