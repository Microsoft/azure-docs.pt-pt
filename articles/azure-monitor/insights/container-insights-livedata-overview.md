---
title: Exibir dados dinâmicos (versão prévia) com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve a exibição em tempo real de logs de kubernetes, eventos e métricas de Pod sem usar kubectl em Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 18ae091a32d0256288d27ad1439ffc7be26db5f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404762"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Como exibir logs de kubernetes, eventos e métricas de pod em tempo real

Azure Monitor para contêineres inclui o recurso de dados dinâmicos (versão prévia), que é um recurso de diagnóstico avançado que permite o acesso direto aos logs de contêiner do AKS (serviço kubernetes do Azure) (stdout/stderr), eventos e métricas Pod. Ele expõe acesso direto a `kubectl logs -c`, `kubectl get` eventos e `kubectl top pods`. Um painel de console mostra os logs, eventos e métricas gerados pelo mecanismo de contêiner para auxiliar ainda mais na solução de problemas em tempo real.

Este artigo fornece uma visão geral detalhada e ajuda você a entender como usar esse recurso. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não têm suporte com esse recurso. Esse recurso depende diretamente do acesso à API kubernetes por meio de um servidor proxy do seu navegador. Habilitar a segurança de rede para bloquear a API do kubernetes desse proxy bloqueará esse tráfego. 

>[!NOTE]
>Esse recurso está disponível em todas as regiões do Azure, incluindo o Azure China. No momento, ele não está disponível no Azure no governo dos EUA.

Para obter ajuda sobre como configurar ou solucionar problemas do recurso de dados dinâmicos (versão prévia), examine nosso [Guia de instalação](container-insights-livedata-setup.md). Esse recurso acessa diretamente a API do kubernetes e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Visão geral da funcionalidade de dados dinâmicos (visualização)

### <a name="search"></a>Search

![Exemplo de filtro do painel do console de dados dinâmicos](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

O recurso de dados dinâmicos (versão prévia) inclui a funcionalidade de pesquisa. No campo de **pesquisa** , você pode filtrar os resultados digitando uma palavra-chave ou termo e quaisquer resultados correspondentes são realçados para permitir uma revisão rápida. Ao exibir eventos, você pode limitar os resultados usando o **filtro** Pill encontrado à direita da barra de pesquisa. Dependendo do recurso que você selecionou, o Pill lista um pod, um namespace ou um cluster do qual escolher.  

### <a name="scroll-lock-and-pause"></a>Scroll Lock e Pause 

Para suspender o AutoScroll e controlar o comportamento do painel, permitindo que você role manualmente os novos dados lidos, você pode usar a opção de **rolagem** . Para reabilitar o AutoScroll, basta selecionar a opção de **rolagem** novamente. Você também pode pausar a recuperação de dados de log ou de evento selecionando a opção **Pause** e, quando estiver pronto para retomar, basta selecionar **reproduzir**.  

![Painel de console de dados dinâmicos pausar exibição dinâmica](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>É recomendável suspender ou pausar o AutoScroll por um curto período de tempo, enquanto soluciona um problema. Essas solicitações podem afetar a disponibilidade e a limitação da API kubernetes no cluster. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante a operação deste recurso. Todas as informações capturadas durante a sessão são excluídas quando você fecha o navegador ou navega para fora dela. Os dados permanecem presentes apenas para visualização dentro da janela de cinco minutos do recurso de métricas; todas as métricas com mais de cinco minutos também são excluídas. O buffer de dados dinâmicos (visualização) consulta dentro dos limites de uso de memória razoáveis (precisa ser mais específico aqui, o que é razoável?). 

## <a name="view-logs"></a>Ver registos

Você pode exibir dados de log em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós**, **controladores**e **contêineres** . Para exibir dados de log, execute as etapas a seguir.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. No painel do cluster AKS, em **monitoramento** no lado esquerdo, escolha **insights**. 

3. Selecione a guia **nós**, **controladores**ou **contêineres** .

4. Selecione um objeto na grade de desempenho e, no painel Propriedades, localizado no lado direito, selecione **exibir dados dinâmicos (visualização)** opção. Se o cluster AKS estiver configurado com logon único usando o Azure AD, você será solicitado a autenticar no primeiro uso durante a sessão do navegador. Selecione a sua conta e a autenticação completa com o Azure.  

    >[!NOTE]
    >Ao exibir os dados do seu espaço de trabalho do Log Analytics selecionando a opção **Exibir no Analytics** no painel Propriedades, os resultados da pesquisa de log mostrarão potencialmente **nós**, **conjuntos de daemon**, **conjuntos de réplicas**, **trabalhos**, **trabalhos cron**, **pods**e **contêineres** que talvez não existam mais. A tentativa de pesquisar logs de um contêiner que não está disponível em `kubectl` também falhará aqui. Examine o recurso [Exibir no Analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre como exibir logs históricos, eventos e métricas.  

Após a autenticação bem-sucedida, o painel de console dados dinâmicos (versão prévia) será exibido abaixo da grade de dados de desempenho, na qual é possível exibir dados de log em um fluxo contínuo. Se o indicador de status de busca mostrar uma marca de seleção verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começam a transmitir para o console.  

![Opção Propriedades do nó exibição de dados do painel](./media/container-insights-livedata-overview/node-properties-pane.png)  

O título do painel mostra o nome do pod com o qual o contêiner é agrupado.

## <a name="view-events"></a>Ver eventos

Você pode exibir dados de eventos em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós**, **controladores**, **contêineres**e **implantações (visualização)** quando um contêiner, Pod, nó, réplicaset, daemonset, trabalho, CronJob ou implantação é selecionado. Para exibir eventos, execute as etapas a seguir.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. No painel do cluster AKS, em **monitoramento** no lado esquerdo, escolha **insights**. 

3. Selecione a guia **nós**, **controladores**, **contêineres**ou **implantações (visualização)** .

4. Selecione um objeto na grade de desempenho e, no painel Propriedades, localizado no lado direito, selecione **exibir dados dinâmicos (visualização)** opção. Se o cluster AKS estiver configurado com logon único usando o Azure AD, você será solicitado a autenticar no primeiro uso durante a sessão do navegador. Selecione a sua conta e a autenticação completa com o Azure.  

    >[!NOTE]
    >Ao exibir os dados do seu espaço de trabalho do Log Analytics selecionando a opção **Exibir no Analytics** no painel Propriedades, os resultados da pesquisa de log mostrarão potencialmente **nós**, **conjuntos de daemon**, **conjuntos de réplicas**, **trabalhos**, **trabalhos cron**, **pods**e **contêineres** que talvez não existam mais. A tentativa de pesquisar logs de um contêiner que não está disponível em `kubectl` também falhará aqui. Examine o recurso [Exibir no Analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre como exibir logs históricos, eventos e métricas.  

Após a autenticação bem-sucedida, o painel de console dados dinâmicos (versão prévia) aparecerá abaixo da grade de dados de desempenho. Se o indicador de status de busca mostrar uma marca de seleção verde, que está na extrema direita do painel, significa que os dados podem ser recuperados e começam a transmitir para o console. 
    
Se o objeto selecionado era um contêiner, selecione a opção **eventos** no painel. Se você selecionou um nó, Pod ou controlador, a exibição de eventos será selecionada automaticamente. 

    ![Controller properties pane view events](./media/container-insights-livedata-overview/controller-properties-live-events.png)  

O título do painel mostra o nome do pod com o qual o contêiner é agrupado.

### <a name="filter-events"></a>Filtrar eventos 

Ao exibir eventos, você pode limitar os resultados usando o **filtro** Pill encontrado à direita da barra de pesquisa. Dependendo do recurso que você selecionou, o Pill lista um pod, um namespace ou um cluster do qual escolher.  

## <a name="view-metrics"></a>Ver métricas 

Você pode exibir dados de métrica em tempo real à medida que eles são gerados pelo mecanismo de contêiner do modo de exibição **nós** ou **controladores** somente quando um **Pod** é selecionado. Para exibir as métricas, execute as etapas a seguir.

1. Na portal do Azure, navegue até o grupo de recursos de cluster AKS e selecione o recurso AKS.

2. No painel do cluster AKS, em **monitoramento** no lado esquerdo, escolha **insights**. 

3. Selecione a guia **nós** ou **controladores** .

4. Selecione um objeto **Pod** na grade desempenho e, no painel Propriedades, localizado no lado direito, selecione **exibir dados dinâmicos (visualização)** opção. Se o cluster AKS estiver configurado com logon único usando o Azure AD, você será solicitado a autenticar no primeiro uso durante a sessão do navegador. Selecione a sua conta e a autenticação completa com o Azure.  

    >[!NOTE]
    >Ao exibir os dados do seu espaço de trabalho do Log Analytics selecionando a opção **Exibir no Analytics** no painel Propriedades, os resultados da pesquisa de log mostrarão potencialmente **nós**, **conjuntos de daemon**, **conjuntos de réplicas**, **trabalhos**, **trabalhos cron**, **pods**e **contêineres** que talvez não existam mais. A tentativa de pesquisar logs de um contêiner que não está disponível em `kubectl` também falhará aqui. Examine o recurso [Exibir no Analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre como exibir logs históricos, eventos e métricas.  

Após a autenticação bem-sucedida, o painel de console dados dinâmicos (versão prévia) aparecerá abaixo da grade de dados de desempenho. Os dados de métrica são recuperados e começam a transmitir para o console para apresentação nos dois gráficos. O título do painel mostra o nome do pod com o qual o contêiner é agrupado.

![Exibir exemplo de métricas de Pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Passos seguintes

- Para continuar a aprender a utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para criar alertas, visualizações ou executar análise adicional de seus clusters.
