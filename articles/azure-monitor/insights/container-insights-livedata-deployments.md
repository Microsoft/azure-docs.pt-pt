---
title: Ver Monitor Azure para contentores Implantações (pré-visualização) / Microsoft Docs
description: Este artigo descreve a visão em tempo real das implementações da Kubernetes sem utilizar kubectl no Monitor Azure para contentores.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75404777"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Como ver implantações (pré-visualização) em tempo real

Com o Monitor Azure para contentores, a função de visualização Deployments (pré-visualização) `kubeclt get deployments` `kubectl describe deployment {your deployment}` emudece o acesso direto aos objetos de implantação kubernetes em tempo real expondo os comandos e comandos. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esta funcionalidade baseia-se no acesso direto à API Kubernetes através de um servidor proxy a partir do seu navegador. Permitir a segurança em rede para bloquear a API kubernetes deste proxy bloqueará este tráfego. 

>[!NOTE]
>Esta funcionalidade está disponível em todas as regiões do Azure, incluindo a Azure China. Atualmente, não está disponível no Governo dos EUA.

Para saber mais, reveja a documentação da Kubernetes sobre [implantações](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Como funciona

A funcionalidade De Dados Ao Vivo (pré-visualização) acede diretamente à API Kubernetes, podendo ser encontradas informações adicionais sobre o modelo de autenticação [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

A função De implantação (pré-visualização) executa uma `/apis/apps/v1/deployments`carga única (refrescada) contra o ponto final das implantações . Permite-lhe selecionar uma determinada implementação e carregar os detalhes de `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`descrever para essa implementação específica contra o ponto final de implantação . 

Selecionar **Refresh** no topo esquerdo da página atualiza a lista de implementação. Isto simula a reexecução do `kubectl` comando. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante o funcionamento desta funcionalidade. Todas as informações captadas durante a sessão são eliminadas quando fecha o seu navegador ou navega para longe dele.  

>[!NOTE]
>Não é possível fixar dados de Dados Vivos (Pré-visualização) da consola para um dashboard Azure.

## <a name="deployments-describe"></a>As implementações descrevem

Para ver os detalhes descritos para `kubectl describe deployment`uma implementação, que é o equivalente a, execute os seguintes passos.

1. No portal Azure, navegue para o grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**. 

3. Selecione o separador **Desmarcações (pré-visualização).**

    ![Vista de implantações no portal Azure](./media/container-insights-livedata-deployments/deployment-view.png)

A vista mostra uma lista de todas as implementações em execução juntamente `kubectl get deployments –all-namespaces`com o espaço de nome e outras informações detalhadas, emular a execução do comando . Pode classificar os resultados selecionando qualquer uma das colunas. 

![Propriedades de implementações painel detalhes](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Quando seleciona uma implementação da lista, um painel de propriedade exibe automaticamente no lado direito da página. Mostra informações relacionadas com a implementação selecionada `kubectl describe deployment {deploymentName}`que veria se executasse o comando . Deve ter reparado que a informação de descrever está a faltar alguns detalhes. Mais notavelmente o **modelo** está em falta. A seleção do separador **Raw** permite-lhe navegar para os detalhes de descrever não analisados.  

![Propriedades de implantação painel detalhes brutos](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Enquanto analisa os detalhes da implementação, pode ver registos e eventos de contentores em tempo real. Selecione a **consola ao vivo do Show** e o painel de consolas Live Data (pré-visualização) aparecerá abaixo da grelha de dados de implementações onde poderá ver os dados de registo ao vivo num fluxo contínuo. Se o indicador de estado de busca mostrar uma marca de verificação verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começa a transmitir para a sua consola.

Também pode filtrar por eventos de espaço de nome ou nível de cluster. Para saber mais sobre os dados de visualização em tempo real na consola, consulte [o View Live Data (pré-visualização) com o Monitor Azure para contentores](container-insights-livedata-overview.md). 

![As implementações vêem dados ao vivo na consola](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Passos seguintes

- Para continuar a aprender a usar o Monitor Azure e monitorizar outros aspetos do seu cluster AKS, consulte a saúde do [Serviço View Azure Kubernetes](container-insights-analyze.md).

- Veja [exemplos](container-insights-log-search.md#search-logs-to-analyze-data) de consultas de registo para ver consultas e exemplos pré-definidos para criar alertas, visualizações ou realizar uma análise mais aprofundada dos seus clusters.
