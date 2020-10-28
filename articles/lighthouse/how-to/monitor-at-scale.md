---
title: Monitorar recursos delegados à escala
description: Aprenda a utilizar eficazmente os Registos do Monitor Azure de forma escalável através dos inquilinos do cliente que está a gerir.
ms.date: 10/26/2020
ms.topic: how-to
ms.openlocfilehash: 3e5c98b3b62a8fbc953a29cf51ac527e5de21110
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735849"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorar recursos delegados à escala

Como prestador de serviços, pode ter a bordo vários inquilinos de clientes para o [Farol de Azure.](../overview.md) O Azure Lighthouse permite que os prestadores de serviços realizem operações em escala em vários inquilinos ao mesmo tempo, tornando as tarefas de gestão mais eficientes.

Este tópico mostra-lhe como usar [os Registos do Monitor Azure](../../azure-monitor/platform/data-platform-logs.md) de uma forma escalável através dos inquilinos do cliente que você está gerindo. Embora nos refiramos a prestadores de serviços e clientes neste tópico, esta orientação também se aplica às [empresas que utilizam o Farol Azure para gerir vários inquilinos.](../concepts/enterprise.md)

> [!NOTE]
> Certifique-se de que os utilizadores dos seus inquilinos de gestão receberam as [funções necessárias para gerir espaços de trabalho do Log Analytics](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) nas subscrições de clientes delegados.

## <a name="create-log-analytics-workspaces"></a>Criar espaços de trabalho log analytics

Para recolher dados, terá de criar espaços de trabalho do Log Analytics. Estes espaços de trabalho Log Analytics são ambientes únicos para dados recolhidos pelo Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho.

Recomendamos a criação destes espaços de trabalho diretamente nos inquilinos do cliente. Desta forma, os seus dados permanecem nos seus inquilinos em vez de serem exportados para os seus. Isto também permite uma monitorização centralizada de quaisquer recursos ou serviços suportados pelo Log Analytics, proporcionando-lhe mais flexibilidade sobre os tipos de dados que monitoriza.

Pode criar um espaço de trabalho Log Analytics utilizando o [portal Azure,](../../azure-monitor/learn/quick-create-workspace.md)utilizando [o Azure CLI,](../../azure-monitor/learn/quick-create-workspace-cli.md)ou utilizando [o Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md).

> [!IMPORTANT]
> Mesmo que todos os espaços de trabalho sejam criados no inquilino do cliente, o fornecedor de recursos Microsoft.Insights também deve ser registado numa subscrição no inquilino gerente.

## <a name="deploy-policies-that-log-data"></a>Implementar políticas que registam dados

Uma vez criado os seus espaços de trabalho Log Analytics, pode implementar [a Política Azure](../../governance/policy/index.yml) através das suas hierarquias de clientes para que os dados de diagnóstico são enviados para o espaço de trabalho apropriado em cada inquilino. As políticas exatas que implementa podem variar consoante os tipos de recursos que pretende monitorizar.

Para saber mais sobre a criação de políticas, consulte [Tutorial: Criar e gerir políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md) Esta [ferramenta comunitária](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) fornece um script para ajudá-lo a criar políticas para monitorizar os tipos específicos de recursos que você escolhe.

Quando tiver determinado quais as políticas a implementar, pode [implantá-las nas suas subscrições delegadas à escala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analisar os dados recolhidos

Depois de implementar as suas políticas, os dados serão registados nos espaços de trabalho do Log Analytics que criou em cada cliente inquilino. Para obter informações em todos os clientes geridos, pode utilizar ferramentas como [os Livros de Trabalho do Azure Monitor](../../azure-monitor/platform/workbooks-overview.md) para recolher e analisar informações de várias fontes de dados. 

## <a name="next-steps"></a>Passos seguintes

- Explore este [livro de amostras construído pelo MVP,](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)que rastreia o relatório de conformidade de remendos [consultando registos de Gestão de Atualização](../../automation/update-management/update-mgmt-query-logs.md) em vários espaços de trabalho do Log Analytics. 
- Saiba mais sobre [o Azure Monitor](../../azure-monitor/index.yml).
- Saiba mais [sobre os Registos do Monitor Azure](../../azure-monitor/platform/data-platform-logs.md).
- Conheça as [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
