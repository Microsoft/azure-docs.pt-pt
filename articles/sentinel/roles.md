---
title: Permissões em Azure Sentinel Microsoft Docs
description: Este artigo explica como o Azure Sentinel utiliza o controlo de acesso baseado em papéis para atribuir permissões aos utilizadores e identifica as ações permitidas para cada função.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587180"
---
# <a name="permissions-in-azure-sentinel"></a>Permissões em Azure Sentinel

O Azure Sentinel utiliza o [Role-Based Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)para fornecer [funções](../role-based-access-control/built-in-roles.md) incorporadas que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Utilizando o RBAC, pode utilizar e criar funções dentro da sua equipa de operações de segurança para garantir o acesso adequado ao Azure Sentinel. Com base nas funções, tem um controlo fino sobre o que os utilizadores com acesso ao Azure Sentinel podem ver. Pode atribuir funções RBAC no espaço de trabalho Do Azure Sentinel diretamente, ou a um grupo de subscrição ou recursos a que o espaço de trabalho pertence.

Existem três funções específicas de Azure Sentinel.  
**Todas as funções integradas do Azure Sentinel concedem acesso aos dados do seu espaço de trabalho Azure Sentinel.**
- [Leitor de Sentinela Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Resposta Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Colaborador azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Além das funções rBAC dedicadas ao Azure Sentinel, existem funções RBAC Azure e Log Analytics que podem conceder um conjunto mais alargado de permissões que incluem acesso ao seu espaço de trabalho Azure Sentinel e outros recursos:

- **Funções Azure:** [Proprietário,](../role-based-access-control/built-in-roles.md#owner) [Colaborador](../role-based-access-control/built-in-roles.md#contributor)e [Leitor.](../role-based-access-control/built-in-roles.md#reader) As funções Azure concedem acesso a todos os seus recursos Azure, incluindo espaços de trabalho Log Analytics e recursos Azure Sentinel.

-   **Funções de Log Analytics:** [Log Analytics contributor,](../role-based-access-control/built-in-roles.md#log-analytics-contributor)Log Analytics [reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). As funções de Log Analytics concedem acesso a todos os seus espaços de trabalho Log Analytics. 

> [!NOTE]
> As funções de Log Analytics também concedem acesso lido em todos os recursos do Azure, mas apenas atribuirão permissões de escrita para os recursos do Log Analytics.


Por exemplo, um utilizador que seja atribuído com funções de **leitor Azure Sentinel** e **colaborador do Azure** (não colaborador **do Azure Sentinel),** poderá editar dados no Azure Sentinel, embora apenas tenham permissões de **leitor Sentinel.** Portanto, se pretender conceder permissões a um único no Azure Sentinel, deve remover cuidadosamente as permissões prévias deste utilizador, certificando-se de que não quebra qualquer função de permissão necessária para outro recurso.

> [!NOTE]
>- O Azure Sentinel usa playbooks para resposta automatizada à ameaça. Os playbooks alavancam as Aplicações Lógicas Azure e são um recurso Azure separado. É possível atribuir membros específicos da sua equipa de operações de segurança com a opção de utilizar aplicações lógicas para operações de orquestração de segurança, automação e resposta (SOAR). Pode utilizar a função de colaborador da [Logic App](../role-based-access-control/built-in-roles.md#logic-app-contributor) ou a função de operador [de Aplicações Lógicas](../role-based-access-control/built-in-roles.md#logic-app-operator) para atribuir permissão explícita para o uso de playbooks.
>- Para adicionar conectores de dados, as funções necessárias para cada conector são por tipo de conector e estão listadas na página do conector relevante. Além disso, para ligar qualquer fonte de dados, deve ter permissão de escrita no espaço de trabalho Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela seguinte exibe papéis e permitiu ações no Azure Sentinel. Um X indica que a ação é permitida para esse papel.

| Função | Criar e executar livros de jogadas| Criar e editar dashboards, regras analíticas e outros recursos do Azure Sentinel | Gerir incidentes (dispensar, atribuir, etc.) | Ver dados, incidentes, dashboards e outros recursos do Azure Sentinel |
|--- |---|---|---|---|
| Leitor de Sentinela Azure | -- | -- | -- | X |
| Resposta Azure Sentinel|--|--| X | X |
| Colaborador azure Sentinel | -- | X | X | X |
| Colaborador do Azure Sentinel + Colaborador da Logic App | X | X | X | X |


> [!NOTE]
> - Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribuir a função de colaborador do Azure Sentinel apenas aos utilizadores que necessitem de criar regras ou dashboards.
> - Recomendamos que estabeleça permissões para o Azure Sentinel no âmbito do grupo de recursos, para que o utilizador possa ter acesso a todos os espaços de trabalho do Azure Sentinel no mesmo grupo de recursos.
>
## <a name="building-custom-rbac-roles"></a>Criar funções RBAC personalizadas

Além de, ou em vez de utilizar funções RBAC incorporadas, pode criar funções RBAC personalizadas para o Azure Sentinel. As funções RBAC personalizadas para o Azure Sentinel são criadas da mesma forma que cria outras funções personalizadas do [Azure RBAC,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) com base em [permissões específicas para o Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) e para [os recursos da Azure Log Analytics.](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>RBAC avançado nos dados armazenados no Azure Sentinel
  
Pode utilizar o controlo avançado de acesso baseado em papel log Analytics através dos dados do seu espaço de trabalho Azure Sentinel. Isto inclui tanto o controlo de acesso baseado em papéis por tipo de dados como o controlo de acesso baseado em recursos. Para obter mais informações sobre as funções de Log Analytics, consulte Gerir dados de registo e espaços de [trabalho no Monitor Azure](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a trabalhar com papéis para utilizadores do Azure Sentinel e o que cada função permite aos utilizadores fazerem.

* [Blog Azure Sentinel.](https://aka.ms/azuresentinelblog) Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
