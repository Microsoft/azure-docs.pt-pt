---
title: Ver métricas em tempo real com o Azure Monitor para contentores Microsoft Docs
description: Este artigo descreve a visão em tempo real das métricas sem usar kubectl com Azure Monitor para recipientes.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 81d7210778fd6b5d75fb4b4fa8e066d2e015174f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85338021"
---
# <a name="how-to-view-metrics-in-real-time"></a>Como ver métricas em tempo real

O Azure Monitor para recipientes Live Data (pré-visualização) permite visualizar métricas sobre o nó e o estado da pod num cluster em tempo real. Emula o acesso direto ao `kubectl top nodes` , e ordena para `kubectl get pods –all-namespaces` `kubectl get nodes` ligar, analisar e visualizar os dados em gráficos de desempenho que estão incluídos com este Insight.

Este artigo fornece uma visão geral detalhada e ajuda-o a entender como usar esta funcionalidade.

>[!NOTE]
>Os clusters AKS ativados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esta funcionalidade baseia-se no acesso direto à API de Kubernetes através de um servidor proxy do seu navegador. Permitir a segurança em rede para bloquear a API de Kubernetes a partir deste proxy bloqueará este tráfego.

Para obter ajuda na configuração ou resolução de problemas da funcionalidade Dados Ao Vivo (pré-visualização), reveja o nosso [guia de configuração](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Como Funciona

A funcionalidade Dados Ao Vivo (pré-visualização) acede diretamente à API de Kubernetes, e informações adicionais sobre o modelo de autenticação podem ser [encontradas aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Esta funcionalidade realiza uma operação de sondagem contra os pontos finais das métricas (incluindo, `/api/v1/nodes` e ), que é a cada cinco `/apis/metrics.k8s.io/v1beta1/nodes` `/api/v1/pods` segundos por defeito. Estes dados estão em cache no seu navegador e são cartografados nos quatro gráficos de desempenho incluídos no Azure Monitor para contentores no **separador Cluster** selecionando **Go Live (pré-visualização)**. Cada sondagem subsequente é traçada numa janela de visualização rolante de cinco minutos.

![Go Ao vivo na vista Cluster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

O intervalo de votação é configurado a partir do intervalo **definido,** permitindo-lhe definir as sondagens para novos dados a cada 1, 5, 15 e 30 segundos.

![Intervalo de sondagens drop-down ao vivo](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Recomendamos que o intervalo de votação seja de um segundo, enquanto se desmente um problema por um curto período de tempo. Estes pedidos podem afetar a disponibilidade e estrangulamento da API de Kubernetes no seu cluster. Depois, reconfigure para um intervalo de votação mais longo.

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante o funcionamento desta função. Todas as informações capturadas durante esta sessão são imediatamente eliminadas quando fecha o seu navegador ou navega para longe da funcionalidade. Os dados só permanecem presentes para visualização dentro da janela de cinco minutos; quaisquer métricas com mais de cinco minutos também são permanentemente eliminadas.

Estes gráficos não podem ser fixados ao último dashboard Azure que viu em modo ao vivo.

## <a name="metrics-captured"></a>Métricas capturadas

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Utilização do NÓ CPU % / Utilização da memória do nó %

Estes dois gráficos de desempenho mapeiam para um equivalente de invocar `kubectl top nodes` e capturar os resultados das colunas **CPU%** e **MEMORY%** para o respetivo gráfico.

![Os principais nosdes de kubectl exemplo resultados](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Gráfico por cento de utilização do CPU de nó](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Gráfico por cento de utilização da memória do nó](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Os cálculos percentil funcionarão em aglomerados maiores para ajudar a identificar os nós mais distantes no seu cluster. Por exemplo, para entender se os nós são subutilizados para fins de escala para baixo. Utilizando a agregação **Min,** pode ver quais os nós que têm baixa utilização no cluster. Para investigar mais aprofundadamente, selecione o **separador Nodes** e selecione a grelha por CPU ou utilização da memória.

Isto também ajuda a entender quais os nós que estão a ser empurrados para os seus limites e se a escala pode ser necessária. A utilização das agregações **Max** e **P95** pode ajudá-lo a ver se existem nós no cluster com alta utilização de recursos. Para mais investigação, mudaria novamente para a conta **Nodes.**

### <a name="node-count"></a>Contagem de nós

Este gráfico de desempenho mapeia para um equivalente a invocar `kubectl get nodes` e mapear a coluna **STATUS** para um gráfico agrupado por tipos de estado.

![Kubectl obtém resultados de exemplo de acenos](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Gráfico de contagem de nódes](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Os nós são relatados num estado **pronto** ou **não pronto.** São contados (e é criada uma contagem total), e os resultados destas duas agregações são cartografados.
Por exemplo, para entender se os seus nós estão a cair em estados falhados. Utilizando a agregação **"Não Pronto",** é possível ver rapidamente o número de nós no seu cluster atualmente no estado **de Não Pronto.**

### <a name="active-pod-count"></a>Contagem de pods ativos

Este gráfico de desempenho mapeia para um equivalente de invocar `kubectl get pods –all-namespaces` e mapear a coluna **STATUS** o gráfico agrupado por tipos de estado.

![Kubectl obtém resultados de exemplo de cápsulas](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Gráfico de contagem de pods de nó](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Os nomes de estado interpretados por `kubectl` podem não corresponder exatamente à tabela.

## <a name="next-steps"></a>Passos seguintes

Consulte [exemplos de consulta de registos](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para criar alertas, visualizações ou realizar uma análise mais aprofundada dos seus clusters.
