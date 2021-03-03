---
title: Ver insights de contentores Implementações (pré-visualização) | Microsoft Docs
description: Este artigo descreve a visão em tempo real das Implementações de Kubernetes sem usar kubectl em insights de contentores.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: c6eaac209234f7352395502c6761312cf258108f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713920"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Como visualizar implementações (pré-visualização) em tempo real

Com os insights do Contentor, a função De visualização de Implementações (pré-visualização) imita o acesso direto aos objetos de implantação de Kubernetes em tempo real expondo os `kubeclt get deployments` comandos e `kubectl describe deployment {your deployment}` comandos.

>[!NOTE]
>Os clusters AKS ativados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esta funcionalidade depende do acesso direto à API Kubernetes através de um servidor proxy do browser. A ativação da segurança de rede para bloquear a API Kubernetes deste proxy bloqueará este tráfego.

Para saber mais, reveja a documentação de Kubernetes sobre [Implementações.](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

## <a name="how-it-works"></a>Como funciona

A funcionalidade Dados Ao Vivo (pré-visualização) acede diretamente à API de Kubernetes, e informações adicionais sobre o modelo de autenticação podem ser [encontradas aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

A função Implementações (pré-visualização) executa uma carga única (re refreshável) contra o ponto final das implementações `/apis/apps/v1/deployments` . Permite-lhe selecionar uma determinada implantação e carregar os detalhes de descrever para essa colocação específica contra o ponto final de implantação `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` .

Selecionar **Refresh** no canto superior esquerdo da página atualiza a lista de implementação. Isto simula a re-execução do `kubectl` comando.

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante o funcionamento desta função. Todas as informações capturadas durante a sessão são eliminadas quando fecha o seu navegador ou navega para longe dele.

>[!NOTE]
>Não é possível fixar dados ao vivo (pré-visualização) da consola para um dashboard Azure.

## <a name="deployments-describe"></a>Implementações descrevem

Para ver Descreva detalhes para uma implantação, que é o equivalente `kubectl describe deployment` a, executar os seguintes passos.

1. No portal Azure, navegue pelo grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**.

3. Selecione o **separador Implementações (pré-visualização).**

    ![Vista de implementações no portal Azure](./media/container-insights-livedata-deployments/deployment-view.png)

A vista mostra uma lista de todas as implementações em execução juntamente com o espaço de nome e outras informações detalhadas, imitando a execução do comando `kubectl get deployments –all-namespaces` . Pode ordenar os resultados selecionando qualquer uma das colunas.

![Implementa detalhes do painel de propriedades](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Quando seleciona uma implementação da lista, um painel de propriedade aparece automaticamente no lado direito da página. Mostra informações relacionadas com a implementação selecionada que veria se executasse o comando `kubectl describe deployment {deploymentName}` . Deve ter reparado que faltam alguns detalhes da informação de desapará-la. Mais notavelmente o **Modelo** está em falta. A seleção do separador **Raw** permite-lhe navegar para os detalhes de desescoditado Descrever.

![Propriedades de implementações pane detalhes brutos](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Ao rever os detalhes da implementação, pode ver registos de contentores e eventos em tempo real. Selecione a **consola ao vivo** e o painel de consola Live Data (pré-visualização) aparecerá abaixo da grelha de dados de implementações onde pode ver dados de registo ao vivo num fluxo contínuo. Se o indicador de estado de busca mostrar uma marca de verificação verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começa a transmitir para a sua consola.

Também pode filtrar por eventos de espaço de nome ou cluster. Para saber mais sobre os dados de visualização em tempo real na consola, consulte [Ver Dados Ao Vivo (pré-visualização) com insights do Contentor](container-insights-livedata-overview.md).

![Implementações visualizam dados ao vivo na consola](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Passos seguintes

- Para continuar a aprender a usar o Azure Monitor e monitorizar outros aspetos do seu cluster AKS, consulte [a saúde do Serviço Ver Azure Kubernetes](container-insights-analyze.md).

- Consulte [exemplos de consulta de registos](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para criar alertas, visualizações ou realizar uma análise mais aprofundada dos seus clusters.
