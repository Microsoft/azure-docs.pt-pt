---
title: Permissões em Azure Sentinel ! Microsoft Docs
description: Este artigo explica como o Azure Sentinel usa o controlo de acesso baseado em funções para atribuir permissões aos utilizadores e identifica as ações permitidas para cada função.
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
ms.openlocfilehash: 9fa10dd1b278b48eb714affb74af59140c8baa09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259342"
---
# <a name="permissions-in-azure-sentinel"></a>Permissões em Azure Sentinel

O Azure Sentinel utiliza [o Controlo de Acesso Baseado em Função (RBAC),](../role-based-access-control/role-assignments-portal.md)para fornecer [funções incorporadas](../role-based-access-control/built-in-roles.md)   que podem ser atribuídas a utilizadores, grupos e serviços em Azure.

Utilizando o RBAC, pode utilizar e criar funções dentro da sua equipa de operações de segurança para conceder acesso adequado ao Azure Sentinel. Com base nos papéis, você tem um controlo fino sobre o que os utilizadores com acesso ao Azure Sentinel podem ver. Pode atribuir funções DE RBAC diretamente no espaço de trabalho Azure Sentinel, ou a um grupo de subscrição ou recursos a que o espaço de trabalho pertence.

Existem três papéis específicos de Azure Sentinel embutidos.  
**Todas as funções incorporadas Azure Sentinel concedem acesso aos dados no seu espaço de trabalho Azure Sentinel.**
- [Azure Sentinel Reader](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel Responder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Colaborador Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Além dos papéis de RBAC dedicados a Azure Sentinel, existem funções de Azure e Log Analytics RBAC que podem conceder um conjunto mais alargado de permissões que incluem acesso ao seu espaço de trabalho Azure Sentinel e outros recursos:

- **Funções Azure:** [Proprietário,](../role-based-access-control/built-in-roles.md#owner) [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)e [Leitor.](../role-based-access-control/built-in-roles.md#reader) As funções Azure concedem acesso a todos os seus recursos Azure, incluindo espaços de trabalho log analytics e recursos Azure Sentinel.

-   **Funções de Log Analytics:** [Log Analytics Contributor](../role-based-access-control/built-in-roles.md#log-analytics-contributor), Log Analytics [Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). As funções de Log Analytics concedem acesso em todos os seus espaços de trabalho Log Analytics. 

> [!NOTE]
> As funções do Log Analytics também concedem acesso à leitura em todos os recursos do Azure, mas apenas atribuirão permissões de escrita aos recursos do Log Analytics.


Por exemplo, um utilizador que seja designado com funções **de Azure Sentinel Reader** e **Azure Contributor** (não **Azure Sentinel Contributor),** poderá editar dados em Azure Sentinel, embora apenas tenham permissões **do Sentinel Reader.** Portanto, se pretender conceder permissões a um utilizador apenas no Azure Sentinel, deve remover cuidadosamente as permissões prévias deste utilizador, certificando-se de que não quebra qualquer papel de permissão necessária para outro recurso.

> [!NOTE]
>- Azure Sentinel usa livros para resposta automatizada de ameaças. Os playbooks aproveitam as Azure Logic Apps e são um recurso Azure separado. É melhor atribuir membros específicos da sua equipa de operações de segurança com a opção de utilizar aplicações lógicas para operações de orquestração, automação e resposta (SOAR). Pode utilizar o papel [de colaborador da Aplicação Lógica](../role-based-access-control/built-in-roles.md#logic-app-contributor) ou o papel de operador da [Aplicação Lógica](../role-based-access-control/built-in-roles.md#logic-app-operator) para atribuir permissão explícita para a utilização de playbooks.
>- Para adicionar conectores de dados, as funções necessárias para cada conector são por tipo de conector e estão listadas na página de conector relevante. Além disso, para ligar qualquer fonte de dados, deve ter permissão de escrita no espaço de trabalho Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela seguinte exibe funções e permitiu ações no Azure Sentinel. Um X indica que a ação é permitida para esse papel.

| Função | Criar e executar livros de reprodução| Criar e editar dashboards, regras analíticas e outros recursos Azure Sentinel | Gerir incidentes (dispensar, atribuir, etc.) | Ver dados, incidentes, dashboards e outros recursos do Azure Sentinel |
|--- |---|---|---|---|
| Azure Sentinel Reader | -- | -- | -- | X |
| Azure Sentinel Responder|--|--| X | X |
| Colaborador Azure Sentinel | -- | X | X | X |
| Azure Sentinel Contributor + Colaborador de Aplicação Lógica | X | X | X | X |


> [!NOTE]
> - Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, atribua o papel de contribuinte Azure Sentinel apenas aos utilizadores que necessitem de criar regras ou dashboards.
> - Recomendamos que desempate permissões para o Azure Sentinel no âmbito do grupo de recursos, para que o utilizador possa ter acesso a todos os espaços de trabalho do Azure Sentinel no mesmo grupo de recursos.
>
## <a name="building-custom-rbac-roles"></a>Criar funções RBAC personalizadas

Além disso, ou em vez de utilizar funções RBAC incorporadas, pode criar papéis RBAC personalizados para Azure Sentinel. Os papéis rbac personalizados para O Azure Sentinel são criados da mesma forma que cria outros papéis [Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) [personalizados, baseados em permissões específicas para Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) e para [recursos Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>RBAC avançado nos dados armazenados no Azure Sentinel
  
Pode utilizar o controlo avançado de acesso baseado em funções do Log Analytics através dos dados do seu espaço de trabalho Azure Sentinel. Isto inclui tanto o controlo de acesso baseado em funções por tipo de dados como o controlo de acesso baseado em funções centrada em recursos. Para obter mais informações sobre as funções de Log Analytics, consulte [Gerir os dados de registo e os espaços de trabalho no Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a trabalhar com papéis para os utilizadores do Azure Sentinel e o que cada papel permite que os utilizadores façam.

* [Azure Sentinel Blog](https://aka.ms/azuresentinelblog). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
