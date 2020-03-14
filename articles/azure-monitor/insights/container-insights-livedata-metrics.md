---
title: Ver métricas em tempo real com o Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve a visão em tempo real das métricas sem usar kubectl com Monitor Azure para recipientes.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216598"
---
# <a name="how-to-view-metrics-in-real-time"></a>Como ver as métricas em tempo real

A funcionalidade Azure Monitor para recipientes Live Data (pré-visualização) permite visualizar métricas sobre o estado do nó e do casulo num cluster em tempo real. Emudeia o acesso direto ao `kubectl top nodes`, `kubectl get pods –all-namespaces`, e `kubectl get nodes` comandos para ligar, analisar e visualizar os dados em gráficos de desempenho que estão incluídos com este Insight. 

Este artigo fornece uma visão geral detalhada e ajuda-o a entender como usar esta funcionalidade.  

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esta funcionalidade baseia-se no acesso direto à API Kubernetes através de um servidor proxy a partir do seu navegador. Permitir a segurança em rede para bloquear a API kubernetes deste proxy bloqueará este tráfego. 

>[!NOTE]
>Esta funcionalidade está disponível em todas as regiões do Azure, incluindo a Azure China. Atualmente, não está disponível no Governo dos EUA.

Para ajudar na configuração ou resolução de problemas da funcionalidade Live Data (pré-visualização), reveja o nosso guia de [configuração](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Como Funciona 

A funcionalidade De Dados Ao Vivo (pré-visualização) acede diretamente à API Kubernetes, podendo ser encontradas informações adicionais sobre o modelo de autenticação [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Esta funcionalidade realiza uma operação de sondagem contra os pontos finais das métricas (incluindo `/api/v1/nodes`, `/apis/metrics.k8s.io/v1beta1/nodes`e `/api/v1/pods`), que é a cada cinco segundos por defeito. Estes dados estão em cache no seu navegador e estão mapeados nas quatro tabelas de desempenho incluídas no Monitor Azure para contentores no separador **Cluster,** selecionando **Go Live (pré-visualização)** . Cada sondagem subsequente é cartografada numa janela de visualização rolante de cinco minutos. 

![Ir ao vivo opção na vista cluster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

O intervalo de votação é configurado a partir do **intervalo set** down, permitindo-lhe definir sondagens para novos dados a cada 1, 5, 15 e 30 segundos. 

![Go Live drop-down intervalo de sondagens](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Recomendamos que o intervalo de votação seja de um segundo, enquanto se despede um problema por um curto período de tempo. Estes pedidos podem afetar a disponibilidade e o estrangulamento da API kubernetes no seu cluster. Depois, reconfigure para um intervalo de votação mais longo. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante o funcionamento desta funcionalidade. Todas as informações captadas durante esta sessão são imediatamente eliminadas quando fecha o seu navegador ou navega para longe da funcionalidade. Os dados só permanecem presentes para visualização dentro da janela de cinco minutos; quaisquer métricas superiores a cinco minutos também são permanentemente eliminadas.

Estes gráficos não podem ser fixados ao último painel de instrumentos Azure que viu em modo ao vivo.

## <a name="metrics-captured"></a>Métricas capturadas

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Utilização do CPU do nó % / Utilização da memória do nó % 

Estes dois gráficos de desempenho mapeiam um equivalente a invocar `kubectl top nodes` e capturar os resultados das colunas **CPU%** e **MEMORY%** para o respetivo gráfico. 

![Kubectl nodes de topo exemplo resultados](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Gráfico de percentagem de utilização do CPU de nódosos](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Gráfico por cento de utilização da memória do nó](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Os cálculos do percentil funcionarão em aglomerados maiores para ajudar a identificar nós mais distantes no seu cluster. Por exemplo, para entender se os nós são subutilizados para fins de redução de escala. Utilizando a agregação **min,** você pode ver quais nós têm baixa utilização no cluster. Para investigar mais aprofundadamente, selecione o separador **Nós** e separe a grelha por CPU ou utilização da memória.

Isto também ajuda a compreender quais os nós que estão a ser empurrados para os seus limites e se a escala for a escala pode ser necessária. Utilizar tanto as agregações **Max** como **P95** pode ajudá-lo a ver se há nós no cluster com alta utilização de recursos. Para mais investigação, voltaria a mudar para a conta de **nós.**

### <a name="node-count"></a>Contagem de nó

Este gráfico de desempenho mapeia um equivalente a invocar `kubectl get nodes` e mapear a coluna **STATUS** para um gráfico agrupado por tipos de estado.

![Kubectl obtém resultados exemplo de nódosos](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Gráfico de contagem de nódosos](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Os nós são relatados num estado **pronto** ou **não pronto.** São contados (e é criada uma contagem total), e os resultados destas duas agregações são cartografados.
Por exemplo, para entender se os seus nós estão caindo em estados falhados. Utilizando a agregação **Não Pronta,** pode ver rapidamente o número de nós no seu cluster atualmente no estado **não pronto.**

### <a name="active-pod-count"></a>Contagem ativa de casulos

Este gráfico de desempenho mapeia um equivalente a invocar `kubectl get pods –all-namespaces` e mapeia a coluna **STATUS** do gráfico agrupado por tipos de estado.

![Kubectl obtém pods exemplo resultados](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Gráfico de contagem de pods de nó](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Os nomes de estado interpretados por `kubectl` podem não corresponder exatamente à tabela. 

## <a name="next-steps"></a>Passos Seguintes

Veja [exemplos](container-insights-log-search.md#search-logs-to-analyze-data) de consultas de registo para ver consultas e exemplos pré-definidos para criar alertas, visualizações ou realizar uma análise mais aprofundada dos seus clusters.
