---
title: Ver a atividade do fornecedor de serviços
description: Os clientes podem ver a atividade registada para ver ações realizadas por prestadores de serviços através da gestão de recursos delegados da Azure.
ms.date: 12/11/2020
ms.topic: how-to
ms.openlocfilehash: 40deca310eea2fc9618cfc83d34caadcf2b2b14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571740"
---
# <a name="view-service-provider-activity"></a>Ver a atividade do fornecedor de serviços

Os clientes que tenham delegado subscrições para [o Azure Lighthouse](../overview.md) podem ver os dados [do registo da Atividade Azure](../../azure-monitor/essentials/platform-logs-overview.md) para ver todas as ações tomadas. Isto dá aos clientes total visibilidade para as operações que os prestadores de serviços estão a realizar através da [gestão de recursos delegados da Azure,](../concepts/azure-delegated-resource-management.md)juntamente com operações feitas pelos utilizadores dentro do inquilino Azure Ative Directory (Azure AD) do cliente.

> [!TIP]
> Fornecemos também definições políticas integradas da Azure Policy para [restringir a delegação a inquilinos de gestão específica](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) e para [auditar a delegação de âmbitos a um inquilino gestor.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json) Para mais informações, consulte [as delegações de auditoria no seu ambiente.](view-manage-service-providers.md#audit-delegations-in-your-environment)

## <a name="view-activity-log-data"></a>Ver dados de registo de atividades

Pode [visualizar o registo](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) de atividades no menu **Monitor** no portal Azure. Para limitar os resultados a uma subscrição específica, utilize os filtros para selecionar uma subscrição específica. Também pode [visualizar e recuperar eventos de registo de atividades](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) programáticamente.

> [!NOTE]
> Os utilizadores do inquilino de um prestador de serviços podem ver os resultados do registo de atividade para uma subscrição delegada num inquilino do cliente se lhes for concedido o papel [de Reader](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclua o acesso ao Leitor) quando essa subscrição foi a bordo do Farol de Azure.

No registo de atividades, verá o nome da operação e o seu estado, juntamente com a data e hora em que foi realizada. O **Evento iniciado por** coluna mostra qual o utilizador que realizou a operação, quer se tratasse de um utilizador no inquilino de um prestador de serviços que atuava através do Farol de Azure, ou de um utilizador no próprio inquilino do cliente. Note que o nome do utilizador é mostrado, em vez do inquilino ou da função que o utilizador foi atribuído para essa subscrição.

A atividade registada está disponível no portal Azure nos últimos 90 dias. Para saber como armazenar estes dados por mais de 90 dias, consulte [recolher e analisar registos de atividades do Azure no espaço de trabalho Log Analytics](../../azure-monitor/essentials/activity-log.md).

> [!NOTE]
> Os utilizadores do prestador de serviços aparecem no registo de atividade, mas estes utilizadores e as suas atribuições de funções não são apresentados no **Controlo de Acesso (IAM)** ou na recuperação de informações de atribuição de funções através de APIs.

## <a name="set-alerts-for-critical-operations"></a>Definir alertas para operações críticas

Para estar atento às operações críticas que os prestadores de serviços (ou utilizadores do seu próprio inquilino) estão a realizar, recomendamos a criação de [alertas de registo de atividades.](../../azure-monitor/alerts/activity-log-alerts.md) Por exemplo, pode querer acompanhar todas as ações administrativas de uma subscrição, ou ser notificado quando qualquer máquina virtual de um determinado grupo de recursos é eliminada. Quando criar alertas, incluirão ações realizadas pelos utilizadores no próprio inquilino do cliente, bem como em qualquer inquilino gerente.

Para obter mais informações, consulte [Criar e gerir alertas de registo de atividades.](../../azure-monitor/alerts/alerts-activity-log.md)

## <a name="create-log-queries"></a>Criar consultas de registo

Pode criar consultas para analisar a sua atividade registada ou focar-se em itens específicos. Por exemplo, talvez uma auditoria exija que você reporte sobre todas as ações de nível administrativo realizadas numa subscrição. Pode criar uma consulta para filtrar apenas estas ações e ordenar os resultados pelo utilizador, data ou outro valor.

Para obter mais informações, consulte [a visão geral das consultas de registo no Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

## <a name="view-user-activity-across-domains"></a>Ver atividade do utilizador em domínios

Pode ver a atividade de utilizadores individuais em vários domínios utilizando os [registos de atividade por livro de amostras de domínio.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain)

Os resultados podem ser filtrados pelo nome de domínio. Também pode aplicar filtros adicionais, tais como categoria, nível ou grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Monitor.](../../azure-monitor/index.yml)
- Saiba como [ver e gerir as ofertas de prestadores de serviços](view-manage-service-providers.md) no portal Azure.
