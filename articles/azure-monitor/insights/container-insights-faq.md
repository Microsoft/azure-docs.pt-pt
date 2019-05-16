---
title: Monitor do Azure para contentores perguntas mais frequentes | Documentos da Microsoft
description: Monitor do Azure para contentores é uma solução que monitoriza o estado de funcionamento dos seus clusters do AKS e instâncias de contentores no Azure. Este artigo responde a perguntas comuns.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: afa332b40884a79b5114b3b8093cd27108c39984
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780013"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Monitor do Azure para contentores perguntas mais frequentes

O FAQ do Microsoft é uma lista de perguntas freqüentes sobre o Monitor do Azure para contentores. Se tiver perguntas adicionais sobre a solução, vá para o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que motivo não vejo dados na minha área de trabalho do Log Analytics?

Se não conseguir ver os dados na área de trabalho do Log Analytics num determinado tempo todos os dias, poderá ter atingido o limite de 500 MB de predefinido ou o limite diário especificado para controlar a quantidade de dados recolhidos diariamente. Quando o limite é cumprido para o dia, o conjunto de dados parada e retomada apenas no dia seguinte. Para rever a utilização de dados e atualizar para um escalão de preço diferente com base nos seus padrões de utilização antecipada, consulte [utilização de dados e o custo de registo](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os Estados de contentor especificados na tabela ContainerInventory?

A tabela de ContainerInventory contém informações sobre contentores parados e em execução. A tabela é preenchida com um fluxo de trabalho dentro do agente que consulta o docker para todos os contentores (em execução e parados) e encaminha esses dados da área de trabalho do Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como posso resolver **registo de subscrição em falta** erro?

Se receber o erro **registo de subscrição em falta para Microsoft.OperationsManagement**, pode resolvê-lo ao registar o fornecedor de recursos **Microsoft.OperationsManagement** no subscrição em que a área de trabalho é definida. Pode encontrar a documentação para saber como fazê-lo [aqui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Existe suporte para clusters do AKS de ativada do RBAC?

A solução de monitorização do contentor não suporta o RBAC, mas é suportada com o Azure Monitor para contentores. Página de detalhes da solução não pode mostrar as informações corretas nos painéis que mostram os dados para estes clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como ativar a recolha de registos de contentores no namespace kube system por meio do Helm?

A recolha de registos de contentores no namespace kube system está desativada por predefinição. Recolha de registos pode ser ativada ao definir uma variável de ambiente a omsagent. Para obter mais informações, consulte a [Monitor do Azure para contentores](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) página do GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como posso atualizar o omsagent para a versão de lançamento mais recente?

Para saber como atualizar o agente, veja [gestão do agente](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Como posso ativar o registo com várias linha?

Atualmente o Azure Monitor para contentores não suporta o registo com várias linha, mas existem soluções alternativas disponíveis. Pode configurar todos os serviços para escrever no formato JSON e, em seguida, Docker/Moby será escrevê-los como uma única linha.

Por exemplo, pode encapsular o início de sessão como um objeto JSON, conforme mostrado no exemplo abaixo para uma aplicação de node. js de exemplo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Estes dados ficará semelhante ao seguinte exemplo no Azure Monitor para os registos quando consulta para o mesmo:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para obter uma visão detalhada do problema, reveja o seguinte [ligação do github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como posso resolver erros do Azure AD quando posso ativar logs dinâmicos? 

Pode ver o erro seguinte: **A resposta do url especificado no pedido não coincide com os urls de resposta configurados para a aplicação: ' < ID da aplicação\>'**. A solução para resolvê-lo pode ser encontrada no artigo [como ver o contentor registos em tempo real com o Azure Monitor para contentores](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não é possível atualizar cluster após a integração?

Se depois de ativar o Azure Monitor para contentores para um cluster do AKS, eliminar a área de trabalho do Log Analytics no cluster foi enviar seus dados, quando tentar atualizar o cluster falhará. Para contornar este problema, terá de desativar a monitorização e, em seguida, reativá-la fazendo referência uma área de trabalho válida diferente na sua subscrição. Quando tentar efetuar a atualização do cluster novamente, ele deve processar e concluída com êxito.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Que portas e domínios é necessário para aberto/lista de permissões para o agente?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>Passos Seguintes

Para começar a monitorizar o seu cluster do AKS, reveja [como para integrar o Azure Monitor para contentores](container-insights-onboard.md) para compreender os requisitos e os métodos disponíveis para ativar a monitorização. 
