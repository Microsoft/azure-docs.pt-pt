---
title: Azure Monitor de perguntas frequentes sobre contêineres | Microsoft Docs
description: Azure Monitor para contêineres é uma solução que monitora a integridade dos clusters AKS e das instâncias de contêiner no Azure. Este artigo responde a perguntas comuns.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/14/2019
ms.openlocfilehash: bda64dd555f1970b70878d827f6be1dab3f1e2d5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555425"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor de perguntas frequentes sobre contêineres

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre Azure Monitor para contêineres. Se você tiver outras dúvidas sobre a solução, vá para o [Fórum de discussão](https://feedback.azure.com/forums/34192--general-feedback) e poste suas perguntas. Quando uma pergunta é frequente, a adicionamos a este artigo para que ela possa ser encontrada de forma rápida e fácil.

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Posso monitorar meu cluster do AKS-Engine com Azure Monitor para contêineres?

Azure Monitor para contêineres dá suporte ao monitoramento de cargas de trabalho de contêiner implantadas em cluster (s) AKS (anteriormente conhecido como ACS-Engine) hospedados no Azure. Para obter mais detalhes e uma visão geral das etapas necessárias para habilitar o monitoramento para esse cenário, consulte [usando Azure monitor para contêineres para AKs-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados em meu espaço de trabalho do Log Analytics?

Se não for possível ver dados no espaço de trabalho Log Analytics em um determinado horário, você pode ter atingido o limite de 500 MB padrão ou o limite diário especificado para controlar a quantidade de dados a serem coletados diariamente. Quando o limite for atingido para o dia, a coleta de dados será interrompida e retomada somente no dia seguinte. Para examinar o uso de dados e atualizar para um tipo de preço diferente com base nos padrões de uso previstos, consulte [uso e custo de dados de log](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os Estados do contêiner especificados na tabela ContainerInventory?

A tabela ContainerInventory contém informações sobre contêineres interrompidos e em execução. A tabela é populada por um fluxo de trabalho dentro do agente que consulta o Docker para todos os contêineres (em execução e parado) e encaminha esses dados para o espaço de trabalho Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como fazer resolver erro de *registro de assinatura ausente* ?

Se você receber o erro **registro de assinatura ausente para Microsoft. OperationsManagement**, você poderá resolvê-lo registrando o provedor de recursos **Microsoft. OperationsManagement** na assinatura em que o espaço de trabalho está definido. A documentação sobre como fazer isso pode ser encontrada [aqui](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Há suporte para clusters AKS habilitados para RBAC?

A solução de monitoramento de contêiner não dá suporte a RBAC, mas tem suporte com Azure Monitor para contêineres. A página de detalhes da solução pode não mostrar as informações corretas nas folhas que mostram dados para esses clusters.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como fazer habilitar a coleta de log para contêineres no namespace do sistema Kube por meio de Helm?

A coleção de logs de contêineres no namespace Kube-System está desabilitada por padrão. A coleta de log pode ser habilitada definindo uma variável de ambiente no omsagent. Para obter mais informações, consulte a página [Azure monitor para contêineres](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) github. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como fazer atualizar o omsagent para a versão de lançamento mais recente?

Para saber como atualizar o agente, consulte [Gerenciamento de agente](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Como fazer habilitar o log de várias linhas?

Atualmente Azure Monitor para contêineres não dá suporte a log de várias linhas, mas há soluções alternativas disponíveis. Você pode configurar todos os serviços para gravar no formato JSON e, em seguida, Docker/Moby os gravará como uma única linha.

Por exemplo, você pode encapsular o log como um objeto JSON, conforme mostrado no exemplo abaixo, para um aplicativo node. js de exemplo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Esses dados serão parecidos com o exemplo a seguir no Azure Monitor para logs quando você fizer a consulta:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para obter uma visão detalhada do problema, examine o [link do GitHub](https://github.com/moby/moby/issues/22920)a seguir.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como fazer resolver erros do Azure AD quando habilito logs dinâmicos? 

Você pode ver o seguinte erro: **a URL de resposta especificada na solicitação não corresponde às URLs de resposta configuradas para o aplicativo: ' < ID do aplicativo \> '** . A solução para solucioná-lo pode ser encontrada no artigo [como exibir logs de contêiner em tempo real com Azure monitor para contêineres](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não posso atualizar o cluster após a integração?

Se, depois de habilitar Azure Monitor para contêineres para um cluster AKS, você excluirá o espaço de trabalho Log Analytics ao qual o cluster estava enviando seus dados, ao tentar atualizar o cluster, ele falhará. Para contornar isso, você precisará desabilitar o monitoramento e reabilitá-lo fazendo referência a um espaço de trabalho válido diferente em sua assinatura. Quando você tenta executar a atualização do cluster novamente, ele deve processar e concluir com êxito.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quais portas e domínios eu preciso abrir/Adicionar à lista de permissões para o agente?
- *. ods.opinsights.azure.com 443
- *. oms.opinsights.azure.com 443
- *. blob.core.windows.net 443
- dc.services.visualstudio.com 443
- *. microsoftonline.com 443
- *. monitoring.azure.com 443
- login.microsoftonline.com 443

## <a name="next-steps"></a>Passos seguintes

Para começar a monitorar o cluster do AKS, examine [como carregar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento. 
