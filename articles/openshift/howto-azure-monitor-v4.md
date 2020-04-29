---
title: Integração do Azure Monitor para o Azure Red Hat OpenShift 4.3
description: Saiba como ativar o Monitor Azure no seu cluster OpenShift do Microsoft Azure Red Hat.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082851"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integração do Azure Monitor para o Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Por favor, note que o Azure Red Hat OpenShift 4.3 está atualmente disponível apenas em pré-visualização privada no Leste dos EUA. A aceitação de pré-visualização privada é apenas por convite. Certifique-se de registar a sua subscrição antes de tentar ativar esta funcionalidade: Registo de [pré-visualização privada Do Chapéu Vermelho Azure OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> As funcionalidades de pré-visualização são autosserviço e são fornecidas como estão disponíveis e estão excluídas do acordo de nível de serviço (SLA) e da garantia limitada. Portanto, as características não são destinadas ao uso da produção.

Este artigo descreve como permitir a pré-visualização privada do Monitor Azure para contentores para clusters OpenShift 4.3 hospedados on-prem ou em qualquer ambiente de nuvem. As mesmas instruções aplicam-se também para ativar a monitorização dos clusters OpenShift (ARO) 4.3 do chapéu vermelho azure.  

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Leme 3](https://helm.sh/docs/intro/install/)
- Acesso ao kubeconfig do aglomerado de kubernetes
- Aceder a uma subscrição do Azure
- Acesso ao cluster OpenShift 4.3 para instalar o monitor Azure para o gráfico de leme de contentores
- Autorização de função RBAC contribuidor mínimo na subscrição do Azure  
- O Agente de Monitorização requer as seguintes portas de saída - e domínios para enviar os dados de monitorização para o backend do Monitor Azure (Se bloqueado por procuração/firewall):
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Inclusão

> [!TIP]
> O guião usa características de bash 4, por isso certifique-se de que a sua festa está atualizada. Pode verificar a sua `bash --version`versão atual com .

### <a name="download-the-onboarding-script"></a>Descarregue o script de embarque

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Execute o seguinte script com azureSubscriptionId, região do espaço de trabalho, clusterName e contexto do cluster Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Por exemplo:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Configure a recolha de dados do agente

Por predefinição, o Monitoragent recolhe os registos de contentores {stdout; stderr} de todos os recipientes em funcionamento em todos os espaços de nome, exceto o sistema kube.  Se pretender configurar a recolha de registos de contentores específica para espaços de nome ou nomes específicos, pode consultar a [configuração](../azure-monitor/insights/container-insights-agent-config.md)do agente Container Insights . Aqui, pode configurar o monitor com as definições de recolha de dados desejadas utilizando o mapa de config.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurar a raspagem das métricas de Prometeu

O Monitor Azure para recipientes raspa as métricas prometheus e ingerir para o backend do Monitor Azure. Consulte a [configuração de Insíptero](../azure-monitor/insights/container-insights-prometheus-integration.md) de Recipiente para obter as instruções de como configurar a raspagem de Prometeu.

Depois de ter sucesso no embarque, navegue para [a Monitorização Híbrida](https://aka.ms/azmon-containers-hybrid) e selecione Ambiente como **"All"** para ver o seu novo cluster OpenShift v4.

## <a name="disable-monitoring"></a>Desativar a monitorização

Se quiser desativar a monitorização, pode eliminar o gráfico de comando do Monitor Azure para contentores, utilizando o seguinte comando para parar de recolher e ingerir dados de monitorização ao Azure Monitor para obter o backend dos contentores.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Monitorização de atualizações

Reexecutar o script de embarque como descrito na secção [Onboarding](#onboarding) com o mesmo parâmetro para atualizar para o mais recente gráfico helm.

## <a name="after-successful-onboarding"></a>Depois de ter sucesso no embarque

Navegue para [a Monitorização Híbrida](https://aka.ms/azmon-containers-hybrid), e poderá ver o seu cluster v4 OpenShift/ARO recém-activado com estado de saúde no separador **Clusters Monitorizados.** Lá, você pode entrar em insights mais profundos, tais como métricas, inventário e logs clicando na coluna **Cluster.**

## <a name="supported-features"></a>Funcionalidades suportadas

Para mais informações sobre as funcionalidades e funcionalidades suportadas, consulte a [visão geral do Container Insights](../azure-monitor/insights/container-insights-overview.md).

Contacte-nos através askcoin@microsoft.com de feedback e perguntas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre monitorização, consulte:
- [Visão geral do Container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Visão geral da consulta de log](../azure-monitor/log-query/log-query-overview.md)
