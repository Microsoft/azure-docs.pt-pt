---
title: Ver a atividade do fornecedor de serviços
description: Os clientes podem ver a atividade registada para ver ações realizadas pelos prestadores de serviços através da gestão de recursos delegados do Azure.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77649641"
---
# <a name="view-service-provider-activity"></a>Ver a atividade do fornecedor de serviços

Os clientes que tenham delegado subscrições para a gestão de recursos delegados do Azure podem ver os dados de [registo da Atividade Azure](../../azure-monitor/platform/platform-logs-overview.md) para ver todas as ações tomadas. Isto dá aos clientes total visibilidade em operações que os prestadores de serviços estão a realizar através da gestão de recursos delegados do Azure, juntamente com operações feitas pelos utilizadores dentro do próprio inquilino azure Ative Directory (Azure AD) do cliente.

> [!TIP]
> Fornecemos também uma definição política integrada da Política Azure para auditar a delegação de âmbitos a um inquilino gestor. Para mais informações, consulte delegações de [auditoria no seu ambiente.](view-manage-service-providers.md#audit-delegations-in-your-environment)

## <a name="view-activity-log-data"></a>Ver dados de registo de atividade

Pode [ver o registo](../../azure-monitor/platform/activity-log-view.md) de atividade do menu **Monitor** no portal Azure. Para limitar os resultados a uma subscrição específica, utilize os filtros para selecionar uma subscrição específica. Também pode [visualizar e recuperar eventos](../../azure-monitor/platform/activity-log-view.md) de registo de atividade sem eprogramágrafos.

> [!NOTE]
> Os utilizadores do inquilino de um prestador de serviços podem visualizar os resultados do registo de atividade para uma subscrição delegada num inquilino de cliente se lhes fosse concedida a função [de Leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função incorporada que inclui o acesso ao Leitor) quando essa subscrição estava a bordo para a gestão de recursos delegados do Azure.

No registo de atividade, verá o nome da operação e o seu estado, juntamente com a data e hora em que foi realizada. O **Evento iniciado por** coluna mostra qual utilizador realizou a operação, quer se trate de um utilizador no inquilino de um prestador de serviços agindo através da gestão de recursos delegados do Azure, ou de um utilizador no próprio inquilino do cliente. Note que o nome do utilizador é mostrado, em vez do inquilino ou a função que o utilizador foi atribuído para essa subscrição.

A atividade registada está disponível no portal Azure nos últimos 90 dias. Para aprender a armazenar estes dados por mais de 90 dias, consulte Recolher e analisar registos de atividade do [Azure no espaço de trabalho do Log Analytics](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Os utilizadores do prestador de serviços aparecem no registo de atividade, mas estes utilizadores e as suas atribuições de funções não são apresentados no Controlo de **Acesso (IAM)** ou na recuperação de informações de atribuição de funções através de APIs.

## <a name="set-alerts-for-critical-operations"></a>Definir alertas para operações críticas

Para se manter atento às operações críticas que os prestadores de serviços (ou utilizadores do seu próprio inquilino) estão a realizar, recomendamos a criação de [alertas](../../azure-monitor/platform/activity-log-alerts.md)de registo de atividades. Por exemplo, pode querer acompanhar todas as ações administrativas para uma subscrição, ou ser notificado quando qualquer máquina virtual de um determinado grupo de recursos for eliminada. Quando criar alertas, incluirão ações realizadas pelos utilizadores no próprio inquilino do cliente, bem como em qualquer managing tenants.

Para mais informações, consulte [Criar e gerir alertas](../../azure-monitor/platform/alerts-activity-log.md)de registo de atividade .

## <a name="create-log-queries"></a>Criar consultas de registo

Pode criar consultas para analisar a sua atividade registada ou focar-se em itens específicos. Por exemplo, talvez uma auditoria exija que informe sobre todas as ações de nível administrativo realizadas numa subscrição. Pode criar uma consulta para filtrar apenas estas ações e classificar os resultados por utilizador, data ou outro valor.

Para mais informações, consulte a [visão geral das consultas de registo no Monitor Azure](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Monitor Azure.](../../azure-monitor/index.yml)
- Saiba como [visualizar e gerir ofertas](view-manage-service-providers.md) de prestadores de serviços no portal Azure.