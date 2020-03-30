---
title: Ver Dados Ao Vivo (pré-visualização) com o Monitor Azure para contentores / Microsoft Docs
description: Este artigo descreve a visão em tempo real de registos, eventos e métricas de cápsulas kubernetes sem usar kubectl no Monitor Azure para recipientes.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216554"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Como ver registos, eventos e métricas de cápsulas kubernetes em tempo real

O Monitor Azure para contentores inclui a funcionalidade Live Data (pré-visualização), que é uma funcionalidade avançada de diagnóstico que lhe permite aceder diretamente aos registos de contentores do Serviço Azure Kubernetes (AKS) (stdout/stderror), eventos e métricas de pod. Expõe o acesso `kubectl logs -c`direto `kubectl get` a `kubectl top pods`eventos e. Um painel de consolamostra os registos, eventos e métricas gerados pelo motor do contentor para ajudar ainda mais em problemas de resolução de problemas em tempo real.

Este artigo fornece uma visão geral detalhada e ajuda-o a entender como usar esta funcionalidade. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esta funcionalidade. Esta funcionalidade baseia-se no acesso direto à API Kubernetes através de um servidor proxy a partir do seu navegador. Permitir a segurança em rede para bloquear a API kubernetes deste proxy bloqueará este tráfego. 

>[!NOTE]
>Esta funcionalidade está disponível em todas as regiões do Azure, incluindo a Azure China. Atualmente, não está disponível no Governo dos EUA.

Para ajudar a configurar ou resolver problemas com a funcionalidade Dados Ao Vivo (pré-visualização), reveja o nosso guia de [configuração](container-insights-livedata-setup.md). Esta funcionalidade acede diretamente à API kubernetes, e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Visão geral da funcionalidade De Dados Ao Vivo (pré-visualização)

### <a name="search"></a>Pesquisa

![Exemplo de filtro de filtro de consola de dados ao vivo](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

A funcionalidade Dados Ao Vivo (pré-visualização) inclui funcionalidade de pesquisa. No campo **De pesquisa,** pode filtrar os resultados digitando uma palavra-chave ou termo e quaisquer resultados correspondentes são destacados para permitir uma revisão rápida. Ao visualizar eventos, pode ainda limitar os resultados utilizando a pílula **filter** encontrada à direita da barra de pesquisa. Dependendo do recurso que selecionou, a pílula lista um Pod, Namespace ou cluster para escolher.  

### <a name="scroll-lock-and-pause"></a>Bloqueio de pergaminho e pausa 

Para suspender o autoscroll e controlar o comportamento do painel, permitindo-lhe percorrer manualmente a nova leitura de dados, pode utilizar a opção **Scroll.** Para reativar o autoscroll, basta selecionar novamente a opção **Scroll.** Também pode interromper a recuperação de dados de registo ou evento selecionando a opção **Pausa** e, quando estiver pronto para retomar, basta selecionar **Reproduzir**.  

![Live Data consola painel pausa vista ao vivo](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Recomendamos apenas suspender ou fazer uma pausa no autoscroll por um curto período de tempo enquanto resoluta problemas. Estes pedidos podem afetar a disponibilidade e o estrangulamento da API kubernetes no seu cluster. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante o funcionamento desta funcionalidade. Todas as informações captadas durante a sessão são eliminadas quando fecha o seu navegador ou navega para longe dele. Os dados só permanecem presentes para visualização dentro da janela de cinco minutos da funcionalidade métrica; as métricas com mais de cinco minutos também são eliminadas. As consultas tampão de dados ao vivo (pré-visualização) dentro de limites razoáveis de utilização da memória (precisam de ser mais específicos aqui, o que é razoável?). 

## <a name="view-logs"></a>Ver registos

Pode visualizar os dados de registo em tempo real à medida que são gerados pelo motor do contentor a partir da vista **Nódosos,** **Controladores**e **Contentores.** Para visualizar os dados de registo, execute os seguintes passos.

1. No portal Azure, navegue para o grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**. 

3. Selecione os **nós,** **controladores**ou **recipientes.**

4. Selecione um objeto da grelha de desempenho e, no painel de propriedades encontrado no lado direito, selecione **ver dados ao vivo (pré-visualização).** Se o cluster AKS estiver configurado com um único sinal de utilização utilizando o Azure AD, é solicitado que autenticar na primeira utilização durante a sessão do navegador. Selecione a sua conta e a autenticação completa com o Azure.  

    >[!NOTE]
    >Ao visualizar os dados do seu espaço de trabalho Log Analytics, selecionando a opção **View in analytics** a partir do painel de propriedades, os resultados da pesquisa de registo mostrarão potencialmente **Nodes**, **Conjuntos Daemon,** **Conjuntos de Réplicas, Empregos,** **Trabalhos cronos,** **Pods**e **Contentores** que podem já não existir. **Jobs** Tentar procurar registos de um recipiente que `kubectl` não esteja disponível também falhará aqui. Reveja a funcionalidade [View in analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre a visualização de registos históricos, eventos e métricas.  

Depois de autenticar com sucesso, o painel de consolas Live Data (pré-visualização) aparecerá abaixo da grelha de dados de desempenho onde poderá ver os dados de registo num fluxo contínuo. Se o indicador de estado de busca mostrar uma marca de verificação verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começa a transmitir para a sua consola.  

![As propriedades do nó painel de dados opção de dados](./media/container-insights-livedata-overview/node-properties-pane.png)  

O título do painel mostra o nome da vagem com a aquecida pelo recipiente.

## <a name="view-events"></a>Ver eventos

Pode visualizar os dados do evento em tempo real à medida que são gerados pelo motor do contentor a partir da vista **Nódosos,** **Controladores,** **Contentores**e **Implementações (pré-visualização)** quando é selecionado um contentor, uma cápsula, nó, ReplicaSet, DaemonSet, trabalho, CronJob ou Deployment. Para visualizar os eventos, execute os seguintes passos.

1. No portal Azure, navegue para o grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**. 

3. Selecione os **nós,** **controladores,** **contentores**ou **abades (pré-visualização).**

4. Selecione um objeto da grelha de desempenho e, no painel de propriedades encontrado no lado direito, selecione **ver dados ao vivo (pré-visualização).** Se o cluster AKS estiver configurado com um único sinal de utilização utilizando o Azure AD, é solicitado que autenticar na primeira utilização durante a sessão do navegador. Selecione a sua conta e a autenticação completa com o Azure.  

    >[!NOTE]
    >Ao visualizar os dados do seu espaço de trabalho Log Analytics, selecionando a opção **View in analytics** a partir do painel de propriedades, os resultados da pesquisa de registo mostrarão potencialmente **Nodes**, **Conjuntos Daemon,** **Conjuntos de Réplicas, Empregos,** **Trabalhos cronos,** **Pods**e **Contentores** que podem já não existir. **Jobs** Tentar procurar registos de um recipiente que `kubectl` não esteja disponível também falhará aqui. Reveja a funcionalidade [View in analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre a visualização de registos históricos, eventos e métricas.  

Depois de autenticar com sucesso, o painel de consolas Live Data (pré-visualização) aparecerá abaixo da grelha de dados de desempenho. Se o indicador de estado de busca mostrar uma marca de verificação verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começa a transmitir para a sua consola. 
    
Se o objeto selecionado ser um recipiente, selecione a opção **Eventos** no painel. Se selecionar um Nó, Pod ou controlador, os eventos de visualização são automaticamente selecionados. 

![Propriedades do controlador painel de eventos](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

O título do painel mostra o nome do Pod com o que o recipiente está agrupado.

### <a name="filter-events"></a>Filtrar eventos 

Ao visualizar eventos, pode ainda limitar os resultados utilizando a pílula **filter** encontrada à direita da barra de pesquisa. Dependendo do recurso que selecionou, a pílula lista um Pod, Namespace ou cluster para escolher.  

## <a name="view-metrics"></a>Ver métricas 

Pode visualizar dados métricos em tempo real à medida que são gerados pelo motor do recipiente a partir da vista **Nódosos** ou **Controladores** apenas quando um **Pod** é selecionado. Para visualizar as métricas, execute os seguintes passos.

1. No portal Azure, navegue para o grupo de recursos de cluster AKS e selecione o seu recurso AKS.

2. No painel de cluster AKS, sob **monitorização** do lado esquerdo, escolha **Insights**. 

3. Selecione o separador **Nós** ou **controladores.**

4. Selecione um objeto **Pod** a partir da grelha de desempenho e no painel de propriedades encontrado no lado direito, selecione **Ver dados ao vivo (pré-visualização).** Se o cluster AKS estiver configurado com um único sinal de utilização utilizando o Azure AD, é solicitado que autenticar na primeira utilização durante a sessão do navegador. Selecione a sua conta e a autenticação completa com o Azure.  

    >[!NOTE]
    >Ao visualizar os dados do seu espaço de trabalho Log Analytics, selecionando a opção **View in analytics** a partir do painel de propriedades, os resultados da pesquisa de registo mostrarão potencialmente **Nodes**, **Conjuntos Daemon,** **Conjuntos de Réplicas, Empregos,** **Trabalhos cronos,** **Pods**e **Contentores** que podem já não existir. **Jobs** Tentar procurar registos de um recipiente que `kubectl` não esteja disponível também falhará aqui. Reveja a funcionalidade [View in analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre a visualização de registos históricos, eventos e métricas.  

Depois de autenticar com sucesso, o painel de consolas Live Data (pré-visualização) aparecerá abaixo da grelha de dados de desempenho. Os dados métricos são recuperados e começam a transmitir para a sua consola para apresentação nos dois gráficos. O título do painel mostra o nome da vagem com a aquecida pelo recipiente.

![Ver exemplo de métricas de Pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Passos seguintes

- Para continuar a aprender a usar o Monitor Azure e monitorizar outros aspetos do seu cluster AKS, consulte a saúde do [Serviço View Azure Kubernetes](container-insights-analyze.md).

- Veja [exemplos](container-insights-log-search.md#search-logs-to-analyze-data) de consultas de registo para ver consultas e exemplos pré-definidos para criar alertas, visualizações ou realizar uma análise mais aprofundada dos seus clusters.
