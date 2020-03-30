---
title: Monitorizar os recursos delegados em escala
description: Aprenda a utilizar eficazmente os Registos do Monitor Azure de forma escalável através dos inquilinos de clientes que está a gerir.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 217df3c55ab54b6569bae8cacb338764ecb7125d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122899"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorizar os recursos delegados em escala

Como prestador de serviços, pode ter embarcado em vários inquilinos de clientes para a gestão de recursos delegados da Azure. [O Azure Lighthouse](../overview.md) permite que os prestadores de serviços realizem operações em escala em vários inquilinos ao mesmo tempo, tornando as tarefas de gestão mais eficientes.

Este tópico mostra-lhe como usar o [Azure Monitor Logs](../../azure-monitor/platform/data-platform-logs.md) de uma forma escalável através dos inquilinos de clientes que você está a gerir.

## <a name="create-log-analytics-workspaces"></a>Criar espaços de trabalho log Analytics

Para recolher dados, terá de criar espaços de trabalho de Log Analytics. Estes espaços de trabalho log Analytics são ambientes únicos para dados recolhidos pelo Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório e configuração de dados, e fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho.

Recomendamos a criação destes espaços de trabalho diretamente nos inquilinos do cliente. Desta forma, os seus dados permanecem nos seus inquilinos em vez de serem exportados para os seus. Isto também permite a monitorização centralizada de quaisquer recursos ou serviços suportados pelo Log Analytics, dando-lhe mais flexibilidade sobre os tipos de dados que monitoriza.

Pode criar um espaço de trabalho log Analytics utilizando o [portal Azure,](../../azure-monitor/learn/quick-create-workspace.md)utilizando [o Azure CLI,](../../azure-monitor/learn/quick-create-workspace-cli.md)ou utilizando o [Azure PowerShell.](../../azure-monitor/learn/quick-create-workspace-posh.md)

## <a name="deploy-policies-that-log-data"></a>Implementar políticas que registam dados

Uma vez criados os seus espaços de trabalho Log Analytics, pode implementar a [Política Azure](../../governance/policy/index.yml) através das hierarquias dos seus clientes para que os dados de diagnóstico sejam enviados para o espaço de trabalho apropriado em cada inquilino. As políticas exatas que implementa podem variar consoante os tipos de recursos que pretende monitorizar.

Para saber mais sobre a criação de políticas, consulte [Tutorial: Criar e gerir políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md) Esta [ferramenta comunitária](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/azure-diagnostics-policy-generator) fornece um script para ajudá-lo a criar políticas para monitorizar os tipos de recursos específicos que escolhe.

Quando tiver determinado quais as políticas a implementar, pode [implantá-las para as suas subscrições delegadas em escala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analisar os dados recolhidos

Depois de ter implementado as suas políticas, os dados serão registados nos espaços de trabalho do Log Analytics que criou em cada inquilino de clientes. Para obter informações sobre todos os clientes geridos, pode utilizar ferramentas como o [Azure Monitor Workbooks](../../azure-monitor/platform/workbooks-overview.md) para recolher e analisar informações de várias fontes de dados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Monitor Azure.](../../azure-monitor/index.yml)
- Saiba mais sobre [os registos do Monitor Azure](../../azure-monitor/platform/data-platform-logs.md).
- Conheça [as experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)
