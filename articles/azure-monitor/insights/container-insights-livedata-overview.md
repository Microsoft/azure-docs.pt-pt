---
title: Ver Dados Ao Vivo (pré-visualização) com monitor Azure para contentores / Microsoft Docs
description: Este artigo descreve a visão em tempo real de registos, eventos e métricas de casulos de Kubernetes sem usar kubectl em Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 9c431cebddb210add496dcca20a0334cc5b12bd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85337954"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Como ver registos, eventos e métricas de kubernetes em tempo real

O Azure Monitor para contentores inclui a funcionalidade Dados Vivos (pré-visualização), que é uma funcionalidade de diagnóstico avançada que lhe permite aceder diretamente aos registos de contentores do Serviço Azure Kubernetes (AKS) (stdout/stderror), eventos e métricas de pod. Expõe o acesso direto a `kubectl logs -c` `kubectl get` eventos, `kubectl top pods` e. Um painel de consola mostra os registos, eventos e métricas gerados pelo motor do contentor para ajudar ainda mais em problemas de resolução de problemas em tempo real.

Este artigo fornece uma visão geral detalhada e ajuda-o a entender como usar esta funcionalidade.

>[!NOTE]
>Os clusters AKS ativados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esta funcionalidade baseia-se no acesso direto à API de Kubernetes através de um servidor proxy do seu navegador. Permitir a segurança em rede para bloquear a API de Kubernetes a partir deste proxy bloqueará este tráfego.

Para ajudar a configurar ou resolver problemas na funcionalidade Dados Ao Vivo (pré-visualização), reveja o nosso [guia de configuração](container-insights-livedata-setup.md). Esta funcionalidade acede diretamente à API de Kubernetes, e informações adicionais sobre o modelo de autenticação podem ser [encontradas aqui.](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)

## <a name="live-data-preview-functionality-overview"></a>Visão geral da funcionalidade de Dados Ao Vivo (pré-visualização)

### <a name="search"></a>Pesquisa

![Exemplo do filtro do painel de consola de dados ao vivo](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

A funcionalidade Dados Ao Vivo (pré-visualização) inclui a funcionalidade de pesquisa. No campo **Procurar,** pode filtrar os resultados digitando uma palavra ou termo chave e quaisquer resultados correspondentes são realçados para permitir uma revisão rápida. Durante a visualização de eventos, pode ainda limitar os resultados utilizando a pílula **Filter** encontrada à direita da barra de pesquisa. Dependendo do recurso selecionado, a pílula lista um Pod, Namespace ou cluster para escolher.

### <a name="scroll-lock-and-pause"></a>Bloqueio de deslocação e pausa

Para suspender o autoscroll e controlar o comportamento do painel, permitindo-lhe deslocar manualmente através da nova leitura de dados, pode utilizar a opção **Scroll.** Para voltar a ativar o autoscroll, basta selecionar novamente a opção **'Scroll'.** Também pode interromper a recuperação de dados de registo ou evento selecionando a opção **Pausa** e quando estiver pronto para retomar, basta selecionar **Reproduzir**.

![Painel de consola de dados ao vivo pausa vista ao vivo](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Recomendamos apenas suspender ou fazer uma pausa no auto-rolo por um curto período de tempo enquanto se desresíam um problema. Estes pedidos podem afetar a disponibilidade e estrangulamento da API de Kubernetes no seu cluster.

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante o funcionamento desta função. Todas as informações capturadas durante a sessão são eliminadas quando fecha o seu navegador ou navega para longe dele. Os dados só permanecem presentes para visualização dentro da janela de cinco minutos da função métrica; quaisquer métricas com mais de cinco minutos também são eliminadas. As consultas de tampão de dados ao vivo (pré-visualização) dentro de limites razoáveis de utilização da memória.

## <a name="view-logs"></a>Ver registos

Pode ver os dados de registo em tempo real, uma vez que são gerados pelo motor do contentor a partir da vista **Dos Nós,** **Controladores**e **Contentores.** Para visualizar os dados de registo, execute os seguintes passos.

1. No portal Azure, navegue pelo grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**.

3. Selecione o **separador Nós,** **Controladores**ou **Contentores.**

4. Selecione um objeto da grelha de desempenho e no painel de propriedades encontrado no lado direito, selecione Ver a opção **de visualização de dados ao vivo (pré-visualização).** Se o cluster AKS estiver configurado com um único sinal de acesso utilizando o Azure AD, é solicitado que autente a autenticar na primeira utilização durante a sessão do navegador. Selecione a sua conta e complete a autenticação com o Azure.

    >[!NOTE]
    >Ao visualizar os dados do seu espaço de trabalho Log Analytics selecionando a opção **Ver em análise** a partir do painel de propriedades, os resultados da pesquisa de registo mostrarão potencialmente **Nós, Conjuntos** **Deemon,** **Conjuntos de Réplicas**, **Empregos,** Cron **Jobs,** **Pods**e **Contentores** que podem já não existir. Tentar procurar registos de um contentor que não esteja disponível `kubectl` também falhará aqui. Reveja o recurso [View in analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre visualização de registos históricos, eventos e métricas.

Após autenticação com sucesso, o painel de consolas Live Data (pré-visualização) aparecerá abaixo da grelha de dados de desempenho, onde pode visualizar os dados de registo num fluxo contínuo. Se o indicador de estado de busca mostrar uma marca de verificação verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começa a transmitir para a sua consola.

![Opções de visualização de pontos de propriedades de nó](./media/container-insights-livedata-overview/node-properties-pane.png)

O título do painel mostra o nome da cápsula com que o recipiente está agrupado.

## <a name="view-events"></a>Ver eventos

Pode ver os dados do evento em tempo real, uma vez que são gerados pelo motor do contentor a partir dos **nós,** **controladores,** **contentores**e **implementações (visualização)** quando é selecionado um contentor, pod, nó, ReplicaSet, DaemonSet, trabalho, CronJob ou Deployment. Para ver eventos, execute os seguintes passos.

1. No portal Azure, navegue pelo grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**.

3. Selecione o **separador Nós,** **Controladores,** **Contentores**ou **Implementações (pré-visualização).**

4. Selecione um objeto da grelha de desempenho e no painel de propriedades encontrado no lado direito, selecione Ver a opção **de visualização de dados ao vivo (pré-visualização).** Se o cluster AKS estiver configurado com um único sinal de acesso utilizando o Azure AD, é solicitado que autente a autenticar na primeira utilização durante a sessão do navegador. Selecione a sua conta e complete a autenticação com o Azure.

    >[!NOTE]
    >Ao visualizar os dados do seu espaço de trabalho Log Analytics selecionando a opção **Ver em análise** a partir do painel de propriedades, os resultados da pesquisa de registo mostrarão potencialmente **Nós, Conjuntos** **Deemon,** **Conjuntos de Réplicas**, **Empregos,** Cron **Jobs,** **Pods**e **Contentores** que podem já não existir. Tentar procurar registos de um contentor que não esteja disponível `kubectl` também falhará aqui. Reveja o recurso [View in analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre visualização de registos históricos, eventos e métricas.

Após a autenticação com sucesso, o painel de consolas Live Data (pré-visualização) aparecerá abaixo da grelha de dados de desempenho. Se o indicador de estado de busca mostrar uma marca de verificação verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começa a transmitir para a sua consola.

Se o objeto selecionado for um recipiente, selecione a opção **Eventos** no painel. Se selecionar um nó, pod ou controlador, os eventos de visualização são automaticamente selecionados.

![Propriedades do controlador vêem eventos](./media/container-insights-livedata-overview/controller-properties-live-event.png)

O título do painel mostra o nome do Pod com o qual o recipiente está agrupado.

### <a name="filter-events"></a>Filtrar eventos

Durante a visualização de eventos, pode ainda limitar os resultados utilizando a pílula **Filter** encontrada à direita da barra de pesquisa. Dependendo do recurso selecionado, a pílula lista um Pod, Namespace ou cluster para escolher.

## <a name="view-metrics"></a>Ver métricas

Pode ver dados métricos em tempo real, uma vez que são gerados pelo motor do contentor a partir da vista **"Nós"** ou **"Controladores" apenas** quando um **Pod** é selecionado. Para ver as métricas, execute os seguintes passos.

1. No portal Azure, navegue pelo grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**.

3. Selecione o **separador Nós** ou **Controladores.**

4. Selecione um objeto **Pod** a partir da grelha de desempenho e no painel de propriedades encontrado no lado direito, selecione Ver a opção **de ver dados ao vivo (pré-visualização).** Se o cluster AKS estiver configurado com um único sinal de acesso utilizando o Azure AD, é solicitado que autente a autenticar na primeira utilização durante a sessão do navegador. Selecione a sua conta e complete a autenticação com o Azure.

    >[!NOTE]
    >Ao visualizar os dados do seu espaço de trabalho Log Analytics selecionando a opção **Ver em análise** a partir do painel de propriedades, os resultados da pesquisa de registo mostrarão potencialmente **Nós, Conjuntos** **Deemon,** **Conjuntos de Réplicas**, **Empregos,** Cron **Jobs,** **Pods**e **Contentores** que podem já não existir. Tentar procurar registos de um contentor que não esteja disponível `kubectl` também falhará aqui. Reveja o recurso [View in analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre visualização de registos históricos, eventos e métricas.

Após a autenticação com sucesso, o painel de consolas Live Data (pré-visualização) aparecerá abaixo da grelha de dados de desempenho. Os dados métricos são recuperados e começam a transmitir para a sua consola para apresentação nos dois gráficos. O título do painel mostra o nome da cápsula com que o recipiente está agrupado.

![Ver exemplo de métricas pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="next-steps"></a>Passos seguintes

- Para continuar a aprender a usar o Azure Monitor e monitorizar outros aspetos do seu cluster AKS, consulte [a saúde do Serviço Ver Azure Kubernetes](container-insights-analyze.md).

- Consulte [exemplos de consulta de registos](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para criar alertas, visualizações ou realizar uma análise mais aprofundada dos seus clusters.
