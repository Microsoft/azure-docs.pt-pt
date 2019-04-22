---
title: Monitorizar o desempenho de cluster do AKS com o Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve como pode ver e analisar os dados de desempenho e de registo com o Azure Monitor para contentores.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: magoedte
ms.openlocfilehash: 3261c2389a9706537366bcd60e00517bbcfb5f48
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426397"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Compreender o desempenho de cluster do AKS com o Azure Monitor para contentores 
Com o Azure Monitor para contentores, pode utilizar os gráficos de desempenho e estado de funcionamento para monitorizar a carga de trabalho de seus clusters do Azure Kubernetes Service (AKS) de duas perspetivas, diretamente a partir de um cluster do AKS ou todos os clusters do AKS numa subscrição do Azure Monitorize. Visualização do Azure Container Instances (ACI) também é possível ao monitorizar um cluster do AKS específico.

Este artigo ajuda-o a compreender a experiência de entre as duas perspetivas e como ajuda-o a avaliar, investigar e resolver problemas detetados.

Para obter informações sobre como ativar o Azure Monitor para contentores, consulte [carregar Monitor do Azure para contentores](container-insights-onboard.md).

O Azure Monitor proporciona uma vista de cluster multi que mostra o estado de funcionamento de todos os clusters do AKS monitorizados implementadas em grupos de recursos nas suas subscrições.  Ela mostra que os clusters do AKS detetado que não são monitorizadas pela solução. Imediatamente que possa compreender o estado de funcionamento do cluster e a partir daqui pode desagregar para a página de desempenho do nó e o controlador ou navegar para ver gráficos de desempenho para o cluster.  Para clusters do AKS detetados e identificado como não monitorizado, pode ativar a monitorização para esse cluster em qualquer altura.  

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [portal do Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Vista de cluster multi do Azure Monitor 
Para ver o estado de funcionamento de todos os clusters do AKS implementados, selecione **Monitor** do painel esquerdo no portal do Azure.  Sob o **Insights** secção, selecione **contentores**.  

![Exemplo de dashboard de cluster multi de Monitor do Azure](./media/container-insights-analyze/azmon-containers-multiview-1018.png)

Sobre o **monitorizados clusters** separador, é possível saber o seguinte:

1. Quantos clusters estão num estado crítico ou mau estado de funcionamento, versus quantas estão em bom estado ou de relatório não (referido como um Estado desconhecido)?
1. São todos meus [motor de Kubernetes do Azure (AKS-engine)](https://github.com/Azure/aks-engine) implementações em bom estado?
1. Quantos nós, o utilizador e os pods de sistema são implementados por cluster.  

Os Estados de estado de funcionamento incluídos são: 

* **Bom estado de funcionamento** – nenhum problema detetado para a VM e está a funcionar conforme necessário. 
* **Crítico** – são detetados um ou mais problemas críticos, que precisam ser abordadas para restaurar o estado operacional normal conforme esperado.
* **Aviso** -forem detetados problemas de um ou mais, que precisam ser abordadas ou a condição de estado de funcionamento pode tornar-se crítico.
* **Desconhecido** – se o serviço não conseguiu estabelecer uma ligação com o nó ou o pod, o estado muda para um Estado desconhecido.
* **Não foi encontrado** - criar a área de trabalho, o grupo de recursos ou subscrição que contém a área de trabalho para esta solução foi eliminada.
* **Não autorizado** -utilizador não tem as permissões necessárias para ler os dados na área de trabalho.
* **Erro** -Ocorreu um erro ao tentar ler dados a partir da área de trabalho.
* **MIS configurados** -Monitor do Azure para contentores não foi configurado corretamente na área de trabalho especificada.
* **Não existem dados** -dados não comunicou à área de trabalho nos últimos 30 minutos.

Estado de funcionamento calcula o estado geral do cluster como *pior de*"os três Estados com uma exceção – se qualquer um dos três Estados for *desconhecido*, irá mostrar o estado geral do cluster **desconhecido**.  

A tabela seguinte fornece uma análise detalhada do cálculo controlando os Estados de funcionamento para um cluster monitorizado na vista de cluster multi.

| |Estado |Disponibilidade |  
|-------|-------|-----------------|  
|**Pod do utilizador**| | |  
| |Bom estado de funcionamento |100% |  
| |Aviso |90 - 99% |  
| |Crítica |< 90% |  
| |Desconhecidos |Se não comunicadas nos últimos 30 minutos |  
|**Pod do sistema**| | |  
| |Bom estado de funcionamento |100% |
| |Aviso |N/A |
| |Crítica |< 100% |
| |Desconhecidos |Se não comunicadas nos últimos 30 minutos |
|**Node** | | |
| |Bom estado de funcionamento |> 85% |
| |Aviso |60 - 84% |
| |Crítica |< 60% |
| |Desconhecidos |Se não comunicadas nos últimos 30 minutos |

Na lista de clusters, pode desagregar para o **Cluster** página, ao clicar no nome do cluster, para o **nós** página de desempenho ao clicar no rollup de nós no **nós** coluna desse cluster específico, ou desagregar para o **controladores** página de desempenho ao clicar no rollup do **pods de utilizador** ou **pods de sistema**coluna.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Ver desempenho diretamente a partir de um cluster do AKS
Acesso para o Azure Monitor para contentores está disponível diretamente a partir de um cluster do AKS, selecionando **Insights** no painel esquerdo. Ver informações sobre o seu cluster do AKS está organizado em quatro perspectivas:

- Cluster
- Nós 
- Controladores  
- Contentores

A página padrão aberto quando clica em **Insights** é **Cluster**, e inclui quatro gráficos de desempenho de linha apresenta métricas chave de desempenho do seu cluster. 

![Exemplos de gráficos de desempenho no separador de Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Gráfico de desempenho apresenta quatro métricas de desempenho:

- **Utilização do nó da CPU&nbsp;%**: Um ponto de vista agregado da utilização da CPU para todo o cluster. Pode filtrar os resultados para o intervalo de tempo, selecionando **Avg**, **Mín**, **Max**, **percentis 50 º**, **90**, e **95** no Seletor de percentis acima do gráfico, seja individualmente ou combinado. 
- **Utilização de memória do nó&nbsp;%**: Um ponto de vista agregado de utilização da memória para todo o cluster. Pode filtrar os resultados para o intervalo de tempo, selecionando **Avg**, **Mín**, **Max**, **percentis 50 º**, **90**, e **95** no Seletor de percentis acima do gráfico, seja individualmente ou combinado. 
- **Contagem de nós**: Uma contagem de nós e o estado do Kubernetes. São Estados de nós do cluster representados *todos os*, *pronto*, e *não está pronto* e podem ser filtrados individualmente ou combinado no Seletor de acima do gráfico. 
- **Contagem de pod de atividade**: Uma contagem de pod e o estado a partir do Kubernetes. Estados de pods representados são *todos os*, *pendente*, *em execução*, e *desconhecido* e podem ser filtrados individualmente ou combinado no Seletor de acima do gráfico. 

Pode usar as teclas de seta esquerda/direita para percorrer cada ponto de dados no gráfico e a seta para cima/para baixo para as chaves para percorrer as linhas de percentil.

Monitor do Azure para contentores também suporta o Azure Monitor [Explorador de métricas](../platform/metrics-getting-started.md), onde pode criar seus próprios gráficos de plotagem, correlacionar e investigar as tendências e afixar nos dashboards. No Explorador de métricas, também pode utilizar os critérios que definiu para visualizar as métricas, como a base de um [métrica com base em regra de alerta](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Ver métricas de contentor no Explorador de métricas
No Explorador de métricas, pode ver o nó agregado e pod métricas de utilização do Azure Monitor para contentores. A tabela seguinte resume os detalhes para ajudar a compreender como utilizar os gráficos de métricas para visualizar as métricas de contentor.

|Espaço de nomes | Métrica |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | PodCount |

Pode aplicar [divisão](../platform/metrics-charts.md#apply-splitting-to-a-chart) de uma métrica para vê-la por dimensão e visualizar como diferentes segmentos do mesmo comparar entre si. Para um nó, pode segmentar o gráfico pela *anfitrião* dimensão, e a partir de um pod pode segmentá-lo pelas seguintes dimensões:

* Controlador
* Espaço de nomes do Kubernetes
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analisar os nós, controladores e estado de funcionamento do contentor

Quando muda para **nós**, **controladores**, e **contentores** separador apresentada automaticamente no lado direito da página é o painel de propriedades.  Mostra as propriedades do item selecionado, incluindo etiquetas definir para organizar os objetos de Kubernetes. Clique nas **>>** ligação no painel para view\hide o painel.  

![Painel de propriedades de perspetivas do exemplo Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

À medida que expande os objetos da hierarquia, as atualizações do painel de propriedades com base no objeto selecionado. No painel, também pode ver eventos de Kubernetes com pesquisas de registos predefinido ao clicar no **registos de eventos de Kubernetes do modo de exibição** link na parte superior do painel. Para obter mais informações sobre a visualização de dados de registo do Kubernetes, consulte [pesquisar registos para analisar dados](#search-logs-to-analyze-data). Enquanto estiver a rever os contentores na **contentores** vista, pode ver registos de contentor em tempo real. Para obter mais informações sobre esta funcionalidade e a configuração necessária para conceder e controlar o acesso, consulte [como ver o contentor registos em tempo real com o Azure Monitor para contentores](container-insights-live-logs.md). 

Utilize o **+ Adicionar filtro** opção na parte superior da página para filtrar os resultados para a vista pelo **Service**, **nó**, ou **espaço de nomes** e o depois Selecionar o âmbito do filtro, em seguida, selecionar a partir de um dos valores mostrados no **selecionar valores** campo.  Após a configuração, o filtro é aplicado globalmente ao visualizar qualquer ponto de vista do AKS cluster.  A fórmula só suporta o sinal de igual.  Pode adicionar filtros adicionais sobre aquela primeira para refinar ainda mais os resultados.  Por exemplo, se tiver especificado um filtro por **nó**, o segundo filtro deve permitir que a seleção **Service** ou **espaço de nomes**.  

![Exemplo usando o filtro para limitar os resultados](./media/container-insights-analyze/add-filter-option-01.png)

Especificar um filtro numa guia continua a ser aplicada ao selecionar outro e é eliminado depois de clicar no **x** símbolo junto ao filtro especificado.   

Mude para o **nós** separador e a hierarquia de linha segue o modelo de objeto do Kubernetes, começando com um nó do cluster. Expanda o nó e pode ver um ou mais pods em execução no nó. Se mais de um contêiner é agrupado para um pod, eles são exibidos como a última linha na hierarquia. Também pode ver a cargas de trabalho relacionadas não pod quantas estão em execução no anfitrião se o anfitrião tiver pressão de memória ou processador.

![Hierarquia de nós do Kubernetes de exemplo na vista de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Azure Container instâncias nós virtuais a executar o SO Linux são apresentados após o último nó de cluster do AKS na lista.  Quando expande um nó Virtual de ACI, pode ver um ou mais ACI pods e os contentores em execução no nó.  As métricas não são recolhidas e comunicadas para nós, apenas os pods.

![Exemplo de hierarquia de nó com o Container Instances listados](./media/container-insights-analyze/nodes-view-aci.png)

A partir de um nó expandido, pode desagregar do pod ou contentor em execução no nó para o controlador para ver os dados de desempenho filtrados para esse controlador. Clique no valor sob a **controlador** coluna para o nó específico.   
![Desagregação de exemplo, a partir do nó para o controlador na vista de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Pode selecionar os controladores ou contentores na parte superior da página e reveja a utilização de estado e de recursos para esses objetos.  Se em vez disso, pretende rever a utilização da memória, na **métrica** na lista pendente, selecione **memória RSS** ou **conjunto de trabalho de memória**. **Memória RSS** só é suportada para Kubernetes versão 1.8 e posterior. Caso contrário, exibir os valores para **Min&nbsp; %**  como *NaN&nbsp;%*, que é um valor de tipo de dados numéricos que representa um indefinido ou valor não representável. 

![Vista de desempenho de nós de contentor](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Por predefinição, os dados de desempenho se baseia em seis últimas horas, mas pode alterar a janela utilizando o **TimeRange** opção no canto superior esquerdo. Também pode filtrar os resultados dentro do intervalo de tempo, selecionando **Avg**, **Mín**, **Max**, **percentis 50 º**, **90**, e **95** no Seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando passa o mouse sobre o gráfico de barras sob o **tendência** coluna, cada barra mostra a utilização da CPU ou memória, dependendo de qual a métrica é selecionada, dentro de um período de exemplo de 15 minutos. Depois de selecionar o gráfico de tendência por meio de um teclado, pode utilizar as teclas Alt + PageUp ou Alt + PageDown para percorrer cada barra individualmente e obter os detalhes do mesmo, tal como faria no mouseover.

![Coloque o cursor do gráfico de barras de tendência ao longo de exemplo](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

No exemplo seguinte, tenha em atenção para o primeiro na lista - nó *aks-nodepool1 -*, o valor de **contentores** é 9, que é um rollup do número total de contentores implementados.

![Rollup de contentores, por exemplo de nó](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Ele pode ajudar a identificar rapidamente se tiver um equilíbrio correto de contentores entre nós do cluster. 

As informações que são apresentadas quando exibir nós são descritas na tabela a seguir:

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do anfitrião. |
| Estado | Vista de Kubernetes do Estado de nó. |
| Média&nbsp;%, Min&nbsp;%, Máx.&nbsp;%, percentis 50 º&nbsp;%, 90&nbsp;% | Média de percentagem de nó com base no percentil durante o período selecionado. |
| AVG, Min, Max, 50th, 90th | Valor real de média de nós com base no percentil de, durante esse período de tempo selecionado. O valor médio é medido desde o limite de CPU/memória definido para um nó; para pods e contentores é o valor de média comunicado pelo host. |
| Contentores | Número de contentores. |
| Tempo de atividade | Representa o tempo, uma vez que um nó iniciado ou foi reiniciado. |
| Controladores | Apenas para os contentores e pods. Mostra qual controlador está residindo no. Nem todos os pods estão num controlador, para alguns podem apresentar **n/d**. | 
| Média de tendência&nbsp;%, Min&nbsp;%, Máx.&nbsp;%, percentis 50 º&nbsp;%, 90&nbsp;% | Tendência de gráfico de barras representa a percentagem de métrica de percentil média do controlador. |

No Seletor de, selecione **controladores**.

![Selecione controladores de exibição](./media/container-insights-analyze/containers-controllers-tab.png)

Aqui pode ver o estado de funcionamento do desempenho dos controladores e controladores de nó Virtual de ACI ou não ligados a um controlador de pods de nó Virtual.

![Vista de desempenho de controladores do < nome >](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia de linha começa com um controlador e quando expande um controlador, exibir os pods de um ou mais.  Expanda o pod, e a última linha apresenta o contentor agrupado para o pod. De um controlador de expandido, pode desagregar para o nó que está a ser executada para ver os dados de desempenho filtrados para esse nó. Não ligados a um controlador de pods ACI estão listados pela última vez na lista.

![Hierarquia de controladores de exemplo com pods de instâncias de contentor listados](./media/container-insights-analyze/controllers-view-aci.png)

Clique no valor sob a **nó** coluna para o controlador específico.   

![Exemplo desagregar a partir do nó de controlador na vista de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações que são apresentadas ao ver controladores são descritas na tabela a seguir:

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | O estado de rollup dos contentores se foi concluída em execução com o estado, tal como *OK*, *Terminated*, *falha* *parado*, ou *Colocada em pausa*. Se o contentor está em execução, mas o estado foi um não está corretamente apresentados ou não foi capturado pelo agente e não tenha respondido a mais de 30 minutos, o estado é *desconhecido*. São fornecidos detalhes adicionais do ícone de estado na tabela abaixo.|
| Média&nbsp;%, Min&nbsp;%, Máx.&nbsp;%, percentis 50 º&nbsp;%, 90&nbsp;% | Agregar média da percentagem média de cada entidade para a métrica selecionada e percentil. |
| AVG, Min, Max, 50th, 90th  | Agregação do média da CPU millicore ou memória desempenho do contentor para o percentil selecionado. O valor médio é medido desde o limite de CPU/memória definido para um pod. |
| Contentores | Número total de contentores para o controlador ou pod. |
| Reinicia | Agregação da contagem de reinício de contentores. |
| Tempo de atividade | Representa o tempo desde o início de um contentor. |
| Nó | Apenas para os contentores e pods. Mostra qual controlador está residindo. | 
| Média de tendência&nbsp;%, Min&nbsp;%, Máx.&nbsp;%, percentis 50 º&nbsp;%, 90&nbsp;%| Tendência de gráfico de barras representa a métrica de percentil média do controlador. |

Os ícones no campo status indicam o estado online dos contentores:
 
| Ícone | Estado | 
|--------|-------------|
| ![Ícone de estado em execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Em execução (pronto)|
| ![Ícone de estado aguardando ou em pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Reportou pela última vez com o ícone de estado](./media/container-insights-analyze/containers-grey-icon.png) | Por fim comunicados em execução, mas ainda não responderam a mais de 30 minutos|
| ![Ícone de estado com êxito](./media/container-insights-analyze/containers-green-icon.png) | Parado ou falha ao parar com êxito|

O ícone de estado apresenta uma contagem com base no que fornece o pod. Mostra os pior dois Estados, e quando paira o rato sobre o status, apresenta um Estado de rollup de todos os pods no contentor. Se não existir um estado estável, o valor de estado apresenta **(0)**. 

No Seletor de, selecione **contentores**.

![Ver os contentores selecionadas](./media/container-insights-analyze/containers-containers-tab.png)

Aqui pode ver o estado de funcionamento do desempenho de seus contentores do Kubernetes do Azure e o Azure Container Instances.  

![Vista de desempenho de controladores do < nome >](./media/container-insights-analyze/containers-containers-view.png)

De um contentor, pode desagregar para um pod ou o nó para ver os dados de desempenho filtrados para esse objeto. Clique no valor sob a **Pod** ou **nó** coluna para o contentor específico.   

![Exemplo desagregar a partir do nó de controlador na vista de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações que são apresentadas quando exibir contêineres são descritas na tabela a seguir:

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | Estado dos contentores, se aplicável. São fornecidos detalhes adicionais do ícone de estado na tabela seguinte.|
| Média&nbsp;%, Min&nbsp;%, Máx.&nbsp;%, percentis 50 º&nbsp;%, 90&nbsp;% | O rollup da percentagem média de cada entidade para a métrica selecionada e percentil. |
| AVG, Min, Max, 50th, 90th  | O rollup do média da CPU millicore ou memória desempenho do contentor para o percentil selecionado. O valor médio é medido desde o limite de CPU/memória definido para um pod. |
| Pod | Contentor onde reside o pod.| 
| Nó |  Nó onde reside o contentor. | 
| Reinicia | Representa o tempo desde o início de um contentor. |
| Tempo de atividade | Representa o tempo, uma vez que um contentor foi iniciado ou reiniciado. |
| Média de tendência&nbsp;%, Min&nbsp;%, Máx.&nbsp;%, percentis 50 º&nbsp;%, 90&nbsp;% | Tendência de gráfico de barras representa a percentagem de métrica de percentil média do contentor. |

Os ícones no campo status indicam os Estados online de pods, conforme descrito na tabela a seguir:
 
| Ícone | Estado |  
|--------|-------------|  
| ![Ícone de estado em execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Em execução (pronto)|  
| ![Ícone de estado aguardando ou em pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|  
| ![Reportou pela última vez com o ícone de estado](./media/container-insights-analyze/containers-grey-icon.png) | Por fim comunicados em execução, mas ainda não responderam em mais de 30 minutos|  
| ![Ícone de estado terminada](./media/container-insights-analyze/containers-terminated-icon.png) | Parado ou falha ao parar com êxito|  
| ![Ícone de estado com falhas](./media/container-insights-analyze/containers-failed-icon.png) | Estado de falha |  


## <a name="container-data-collection-details"></a>Detalhes de recolha de dados de contentor
Informações de contentor recolhe diversos dados de registo e métricas de desempenho de anfitriões de contentor e contentores. Dados são recolhidos a cada três minutos.

### <a name="container-records"></a>Registos de contentor

Exemplos de registos que são recolhidos pelo Monitor do Azure para contentores e os tipos de dados que aparecem nos resultados de pesquisa de registos são apresentados na tabela a seguir:

| Tipo de dados | Tipo de dados na pesquisa de registos | Campos |
| --- | --- | --- |
| Desempenho para anfitriões e contentores | `Perf` | CounterName de computador, ObjectName, &#40;% de tempo do processador, o disco lê MB, disco escreve MB, MB de utilização de memória, rede receba Bytes, rede enviar Bytes, o processador de seg de utilização, rede&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contentor | `ContainerInventory` | TimeGenerated, computador, nome do contentor, ContainerHostname, imagem, ImageTag, ContainerState, ExitCode, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ID do contentor, ImageID |
| Inventário de imagens de contentor | `ContainerImageInventory` | TimeGenerated, computador, imagem, ImageTag, ImageSize, VirtualSize, em execução, em pausa, parado, falha, SourceSystem, ImageID, TotalContainer |
| Registo de contentor | `ContainerLog` | TimeGenerated, o computador, o ID de imagem, o nome do contentor, LogEntrySource, LogEntry, SourceSystem, ID do contentor |
| Registo do serviço de contentor | `ContainerServiceLog`  | TimeGenerated, computador, TimeOfCommand, imagem, comando, SourceSystem, ID do contentor |
| Inventário de nó do contentor | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Processo de contentor | `ContainerProcess_CL` | TimeGenerated, computador, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventário de pods num cluster do Kubernetes | `KubePodInventory` | TimeGenerated, computador, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ID do contentor, ContainerName, nome, PodLabel, espaço de nomes, PodStatus, ClusterName PodIp, SourceSystem |
| Inventário de parte de nós de um cluster de Kubernetes | `KubeNodeInventory` | TimeGenerated, computador, ClusterName, ClusterId, LastTransitionTimeReady, etiquetas, estado, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents_CL` | TimeGenerated, computador, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, mensagem, SourceSystem | 
| Serviços no cluster do Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SNode" | CounterName de computador, ObjectName, &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de desempenho para a parte de contentores de cluster do Kubernetes | Desempenho &#124; onde ObjectName = = "K8SContainer" | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Registos de pesquisa para analisar dados
O log Analytics pode ajudá-lo a analisar as tendências, diagnosticar afunilamentos, previsão, ou correlacionar dados que podem ajudar a determinam se a configuração de cluster atual é satisfatória. Pesquisas de registos predefinidas são fornecidas por si para começar imediatamente a utilizar ou personalizar para retornar as informações da maneira como desejar. 

Pode efetuar análises interativas de dados na área de trabalho ao selecionar o **registos de eventos de Kubernetes do modo de exibição** ou **ver registos de contentor** opção no painel de pré-visualização. O **pesquisa de registos** é apresentada a página à direita da página do portal do Azure que estavam no.

![Analisar dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

A saída de registos de contentor é reencaminhada para o Log Analytics são STDOUT e STDERR. Porque o Azure Monitor está a monitorizar o Kubernetes gerido pelo Azure (AKS), o sistema do Kube não é coletado hoje devido ao grande volume de dados gerados. 

### <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo
Muitas vezes é útil criar consultas que começam com um ou dois exemplos em, em seguida, modificá-las para satisfazer as suas necessidades. Para ajudar a criar consultas mais avançadas, pode experimentar com as seguintes consultas de exemplo:

| Consulta | Descrição | 
|-------|-------------|
| ContainerInventory<br> &#124;Computador, nome, imagem, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime do projeto<br> &#124;tabela de composição | Listar todas as informações de ciclo de vida de um contentor| 
| KubeEvents_CL<br> &#124;onde not(isempty(Namespace_s))<br> &#124;Ordenar por TimeGenerated desc<br> &#124;tabela de composição | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124;resumir AggregatedValue = count () by imagem, ImageTag, está em execução | Inventário de imagens | 
| **Selecione a opção de exibição de gráfico de linha**:<br> Desempenho<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "cpuUsageNanoCores" &#124; resumir AvgCPUUsageNanoCores = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Contentor da CPU | 
| **Selecione a opção de exibição de gráfico de linha**:<br> Desempenho<br> &#124;onde ObjectName = = "K8SContainer" e CounterName = = "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg(CounterValue) por bin (TimeGenerated, 30m), InstanceName | Memória de contentor |

## <a name="next-steps"></a>Passos Seguintes
Monitor do Azure para contentores não inclui um conjunto predefinido de alertas para copiar e modificar de acordo com seus processos e procedimentos de suporte. Reveja os [criar alertas de desempenho com o Azure Monitor para contentores](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória.  
