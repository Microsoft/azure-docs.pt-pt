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
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418626"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Monitor do Azure para contentores perguntas mais frequentes
O FAQ do Microsoft é uma lista de perguntas freqüentes sobre o Monitor do Azure para contentores. Se tiver perguntas adicionais sobre a solução, vá para o [fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>Por que não estou a ver dados se a área de trabalho do Log Analytics é configurada com o escalão de preço gratuito? 

Poderá ter atingido o limite de 500 MB de predefinição ou especificado um máximo diário para controlar a quantidade de dados recolhidos diariamente. Para verificar e gerir a utilização de dados, consulte [utilização de dados e o custo de registo](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Quais são os Estados de contentores especificadas na tabela ContainerInventory?
A tabela de ContainerInventory contém informações sobre contentores parados e em execução. A tabela é preenchida com um fluxo de trabalho dentro do agente que consulta o docker para todos os contentores (em execução e parados) e encaminha esses dados da área de trabalho do Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Como posso resolver erros relacionados com **registo de subscrição em falta para Microsoft.OperationsManagement**?
Para resolver o erro, registe o fornecedor de recursos **Microsoft.OperationsManagement** na subscrição em que a área de trabalho é definida. Pode encontrar a documentação para saber como fazê-lo [aqui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>O Monitor do Azure para contentores inclui suporte para clusters do AKS de ativada do RBAC?
RBAC ativada AKS clusters não são atualmente suportados pela solução. Página de detalhes da solução não pode mostrar as informações corretas nos painéis que mostram os dados para estes clusters.

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

Estes dados ficará semelhante ao seguinte no Azure Monitor para os registos quando consulta para o mesmo:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para obter uma visão detalhada do problema, reveja o seguinte [ligação do github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Como posso resolver erros de Azure Active Directory ao ativar a logs dinâmicos? 
Pode ver o erro seguinte: **A resposta do url especificado no pedido não coincide com os urls de resposta configurados para a aplicação: '60b4dec7-5a69-4165-a211-12c40b5c0435'**. A correção para que isso pode ser encontrada no artigo [como ver o contentor registos em tempo real com o Azure Monitor para contentores](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Passos Seguintes
Para começar a monitorizar o seu cluster do AKS, reveja [como para integrar o Azure Monitor para contentores](container-insights-onboard.md) para compreender os requisitos e os métodos disponíveis para ativar a monitorização. 