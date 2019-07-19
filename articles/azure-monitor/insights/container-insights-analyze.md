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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: dc55e4999a09c45463ae75b05d610b290f5ff526
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248322"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Compreender o desempenho de cluster do AKS com o Azure Monitor para contentores 
Com o Azure Monitor para contentores, pode utilizar os gráficos de desempenho e estado de funcionamento para monitorizar a carga de trabalho de seus clusters do Azure Kubernetes Service (AKS) de duas perspetivas, diretamente a partir de um cluster do AKS ou todos os clusters do AKS numa subscrição do Azure Monitorize. Visualização do Azure Container Instances (ACI) também é possível ao monitorizar um cluster do AKS específico.

Este artigo ajuda-o a compreender a experiência de entre as duas perspetivas e como ajuda-o a avaliar, investigar e resolver problemas detetados.

Para obter informações sobre como ativar o Azure Monitor para contentores, consulte [carregar Monitor do Azure para contentores](container-insights-onboard.md).

Azure Monitor fornece uma exibição de vários clusters mostrando o status de integridade de todos os clusters AKS monitorados que executam o Linux e o Windows Server 2019 implantados em grupos de recursos em suas assinaturas.  Ela mostra que os clusters do AKS detetado que não são monitorizadas pela solução. Imediatamente que possa compreender o estado de funcionamento do cluster e a partir daqui pode desagregar para a página de desempenho do nó e o controlador ou navegar para ver gráficos de desempenho para o cluster.  Para clusters do AKS detetados e identificado como não monitorizado, pode ativar a monitorização para esse cluster em qualquer altura.  

As principais diferenças de monitoramento de um cluster do Windows Server com Azure Monitor para contêineres em comparação com um cluster do Linux são as seguintes:

- A métrica de RSS de memória não está disponível para o nó e contêineres do Windows.
- As informações de capacidade de armazenamento em disco não estão disponíveis para nós do Windows.
- O suporte a logs dinâmicos está disponível com exceção dos logs de contêiner do Windows.
- Somente ambientes de Pod são monitorados, não ambientes de Docker.
- Com a versão de visualização, há suporte para um máximo de 30 contêineres do Windows Server. Essa limitação não se aplica a contêineres do Linux.  

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [portal do Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Vista de cluster multi do Azure Monitor 
Para ver o estado de funcionamento de todos os clusters do AKS implementados, selecione **Monitor** do painel esquerdo no portal do Azure.  Sob o **Insights** secção, selecione **contentores**.  

![Exemplo de dashboard de cluster multi de Monitor do Azure](./media/container-insights-analyze/azmon-containers-multiview.png)

Sobre o **monitorizados clusters** separador, é possível saber o seguinte:

1. Quantos clusters estão num estado crítico ou mau estado de funcionamento, versus quantas estão em bom estado ou de relatório não (referido como um Estado desconhecido)?
2. São todos meus [motor de Kubernetes do Azure (AKS-engine)](https://github.com/Azure/aks-engine) implementações em bom estado?
3. Quantos nós, usuários e pods de sistema são implantados por cluster?
4. Quanto espaço em disco está disponível e há um problema de capacidade?

Os Estados de estado de funcionamento incluídos são: 

* **Bom estado de funcionamento** – nenhum problema detetado para a VM e está a funcionar conforme necessário. 
* **Crítico** – são detetados um ou mais problemas críticos, que precisam ser abordadas para restaurar o estado operacional normal conforme esperado.
* **Aviso** -forem detetados problemas de um ou mais, que precisam ser abordadas ou a condição de estado de funcionamento pode tornar-se crítico.
* **Desconhecido** – se o serviço não conseguiu estabelecer uma ligação com o nó ou o pod, o estado muda para um Estado desconhecido.
* **Não foi encontrado** - criar a área de trabalho, o grupo de recursos ou subscrição que contém a área de trabalho para esta solução foi eliminada.
* **Não autorizado** -utilizador não tem as permissões necessárias para ler os dados na área de trabalho.
* **Erro** -Ocorreu um erro ao tentar ler dados a partir da área de trabalho.
* Configurado incorretamente **-Azure monitor** para contêineres não foi configurado adequadamente no espaço de trabalho especificado.
* **Não existem dados** -dados não comunicou à área de trabalho nos últimos 30 minutos.

O estado de integridade calcula o status geral do cluster como o *pior dos* três Estados com uma exceção – se qualquer um dos três Estados for *desconhecido*, o estado geral do cluster mostrará **desconhecido**.  

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

- Utilização de CPU do nó: **&nbsp;%** Uma perspectiva agregada da utilização da CPU para todo o cluster. Pode filtrar os resultados para o intervalo de tempo, selecionando **Avg**, **Mín**, **Max**, **percentis 50 º**, **90**, e **95** no Seletor de percentis acima do gráfico, seja individualmente ou combinado. 
- Utilização de memória do nó: **&nbsp;%** Uma perspectiva agregada de utilização de memória para todo o cluster. Pode filtrar os resultados para o intervalo de tempo, selecionando **Avg**, **Mín**, **Max**, **percentis 50 º**, **90**, e **95** no Seletor de percentis acima do gráfico, seja individualmente ou combinado. 
- **Contagem de nós**: Uma contagem de nós e status de kubernetes. São Estados de nós do cluster representados *todos os*, *pronto*, e *não está pronto* e podem ser filtrados individualmente ou combinado no Seletor de acima do gráfico. 
- **Contagem de pod de atividade**: Uma contagem de Pod e o status de kubernetes. Estados de pods representados são *todos os*, *pendente*, *em execução*, e *desconhecido* e podem ser filtrados individualmente ou combinado no Seletor de acima do gráfico. 

Você pode usar as teclas de seta para a esquerda/direita para percorrer cada ponto de dados no gráfico e as teclas de seta para cima/para baixo para percorrer as linhas percentuais. Clicar no ícone de pino no canto superior direito de qualquer um dos gráficos fixará o gráfico selecionado no último painel do Azure que você exibiu pela última vez. No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel irá redirecioná-lo para Azure Monitor para contêineres e carregar o escopo e a exibição corretos.

O Azure Monitor para contêineres também dá suporte a Azure Monitor [métricas Explorer](../platform/metrics-getting-started.md), onde você pode criar seus próprios gráficos de plotagem, correlacionar e investigar tendências e fixar em painéis. No Metrics Explorer, você também pode usar os critérios definidos para visualizar suas métricas como a base de uma regra de [alerta baseada em métrica](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Exibir métricas de contêiner no Metrics Explorer
No Metrics Explorer, você pode exibir as métricas de utilização de nó e Pod agregadas de Azure Monitor para contêineres. A tabela a seguir resume os detalhes para ajudá-lo a entender como usar os gráficos de métrica para visualizar as métricas de contêiner.

|Espaço de Nomes | Métrica |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| percepções. Container/pods | |
| | PodCount |

Você pode aplicar a [divisão](../platform/metrics-charts.md#apply-splitting-to-a-chart) de uma métrica para exibi-la por dimensão e visualizar como os diferentes segmentos de ti se comparam entre si. Para um nó, você pode segmentar o gráfico pela dimensão do *host* e, a partir de um pod, você pode segmentá-lo pelas seguintes dimensões:

* Controle
* Namespace kubernetes
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analisar nós, controladores e integridade do contêiner

Quando muda para **nós**, **controladores**, e **contentores** separador apresentada automaticamente no lado direito da página é o painel de propriedades. Ele mostra as propriedades do item selecionado, incluindo os rótulos que você define para organizar objetos kubernetes. Quando um nó do Linux é selecionado, ele também é mostrado na seção espaço em disco disponível no **disco local** e percentual usado para cada disco apresentado ao nó. Clique nas **>>** ligação no painel para view\hide o painel. 

![Painel de propriedades de perspetivas do exemplo Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

À medida que expande os objetos da hierarquia, as atualizações do painel de propriedades com base no objeto selecionado. No painel, também pode ver eventos de Kubernetes com pesquisas de registos predefinido ao clicar no **registos de eventos de Kubernetes do modo de exibição** link na parte superior do painel. Para obter mais informações sobre a visualização de dados de registo do Kubernetes, consulte [pesquisar registos para analisar dados](container-insights-log-search.md). Ao examinar os recursos de cluster, você pode ver logs de contêiner e eventos em tempo real. Para obter mais informações sobre esse recurso e a configuração necessária para conceder e controlar o acesso, consulte [como exibir logs em tempo real com o Azure monitor para contêineres](container-insights-live-logs.md). 

Use a opção **+ Adicionar filtro** na parte superior da página para filtrar os resultados da exibição por **serviço**, **nó**, **namespace**ou pool de **nós** e, depois de selecionar o escopo do filtro, selecione um dos valores mostrados no **Selecione o campo valor (es)** .  Após a configuração, o filtro é aplicado globalmente ao visualizar qualquer ponto de vista do AKS cluster.  A fórmula só suporta o sinal de igual.  Pode adicionar filtros adicionais sobre aquela primeira para refinar ainda mais os resultados.  Por exemplo, se tiver especificado um filtro por **nó**, o segundo filtro deve permitir que a seleção **Service** ou **espaço de nomes**.  

![Exemplo usando o filtro para limitar os resultados](./media/container-insights-analyze/add-filter-option-01.png)

Especificar um filtro numa guia continua a ser aplicada ao selecionar outro e é eliminado depois de clicar no **x** símbolo junto ao filtro especificado.   

Mude para o **nós** separador e a hierarquia de linha segue o modelo de objeto do Kubernetes, começando com um nó do cluster. Expanda o nó e pode ver um ou mais pods em execução no nó. Se mais de um contêiner é agrupado para um pod, eles são exibidos como a última linha na hierarquia. Também pode ver a cargas de trabalho relacionadas não pod quantas estão em execução no anfitrião se o anfitrião tiver pressão de memória ou processador.

![Hierarquia de nós do Kubernetes de exemplo na vista de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Os contêineres do Windows Server que executam o sistema operacional Windows Server 2019 são mostrados após todos os nós baseados em Linux na lista. Ao expandir um nó do Windows Server, você pode exibir um ou mais pods e contêineres em execução no nó. Quando um nó é selecionado, o painel Propriedades mostra informações de versão, excluindo as informações do agente, pois os nós do Windows Server não têm um agente instalado.  

![Hierarquia de nós de exemplo com nós do Windows Server listados](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container instâncias nós virtuais a executar o SO Linux são apresentados após o último nó de cluster do AKS na lista.  Quando expande um nó Virtual de ACI, pode ver um ou mais ACI pods e os contentores em execução no nó.  As métricas não são recolhidas e comunicadas para nós, apenas os pods.

![Exemplo de hierarquia de nó com o Container Instances listados](./media/container-insights-analyze/nodes-view-aci.png)

A partir de um nó expandido, pode desagregar do pod ou contentor em execução no nó para o controlador para ver os dados de desempenho filtrados para esse controlador. Clique no valor sob a **controlador** coluna para o nó específico.   
![Desagregação de exemplo, a partir do nó para o controlador na vista de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Pode selecionar os controladores ou contentores na parte superior da página e reveja a utilização de estado e de recursos para esses objetos.  Se em vez disso, pretende rever a utilização da memória, na **métrica** na lista pendente, selecione **memória RSS** ou **conjunto de trabalho de memória**. **Memória RSS** só é suportada para Kubernetes versão 1.8 e posterior. Caso contrário, exibir os valores para **Min&nbsp; %**  como *NaN&nbsp;%* , que é um valor de tipo de dados numéricos que representa um indefinido ou valor não representável.

Conjunto de trabalho de memória mostra a memória residente e a memória virtual (cache) incluídas e é um total do que o aplicativo está usando. A memória RSS mostra apenas a memória principal, que é a memória residente. Essa métrica está mostrando a capacidade real da memória disponível.

![Vista de desempenho de nós de contentor](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Por predefinição, os dados de desempenho se baseia em seis últimas horas, mas pode alterar a janela utilizando o **TimeRange** opção no canto superior esquerdo. Também pode filtrar os resultados dentro do intervalo de tempo, selecionando **Avg**, **Mín**, **Max**, **percentis 50 º**, **90**, e **95** no Seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando passa o mouse sobre o gráfico de barras sob o **tendência** coluna, cada barra mostra a utilização da CPU ou memória, dependendo de qual a métrica é selecionada, dentro de um período de exemplo de 15 minutos. Depois de selecionar o gráfico de tendências por meio de um teclado, você pode usar as teclas Alt + PageUp ou ALT + PageDown para percorrer cada barra individualmente e obter os mesmos detalhes que você faria no passo a passo.

![Exemplo de foco de gráfico de barra de tendência](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

No exemplo seguinte, tenha em atenção para o primeiro na lista - nó *aks-nodepool1 -* , o valor de **contentores** é 9, que é um rollup do número total de contentores implementados.

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

![\<Exibição de desempenho de controladores de > de nome](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia de linha começa com um controlador e quando expande um controlador, exibir os pods de um ou mais.  Expanda o pod, e a última linha apresenta o contentor agrupado para o pod. De um controlador de expandido, pode desagregar para o nó que está a ser executada para ver os dados de desempenho filtrados para esse nó. Não ligados a um controlador de pods ACI estão listados pela última vez na lista.

![Hierarquia de controladores de exemplo com pods de instâncias de contentor listados](./media/container-insights-analyze/controllers-view-aci.png)

Clique no valor sob a **nó** coluna para o controlador específico.   

![Exemplo desagregar a partir do nó de controlador na vista de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações que são apresentadas ao ver controladores são descritas na tabela a seguir:

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | O estado de rollup dos contentores se foi concluída em execução com o estado, tal como *OK*, *Terminated*, *falha* *parado*, ou *Colocada em pausa*. Se o contentor está em execução, mas o estado foi um não está corretamente apresentados ou não foi capturado pelo agente e não tenha respondido a mais de 30 minutos, o estado é *desconhecido*. São fornecidos detalhes adicionais do ícone de estado na tabela abaixo.|
| Média&nbsp;%, Min&nbsp;%, Máx.&nbsp;%, percentis 50 º&nbsp;%, 90&nbsp;% | Média acumulada do percentual médio de cada entidade para a métrica selecionada e o percentil. |
| AVG, Min, Max, 50th, 90th  | Acumular o desempenho médio de CPU millicore ou memória do contêiner para o percentil selecionado. O valor médio é medido desde o limite de CPU/memória definido para um pod. |
| Contentores | Número total de contentores para o controlador ou pod. |
| Reinicia | Acumular a contagem de reinicialização de contêineres. |
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

O ícone de estado apresenta uma contagem com base no que fornece o pod. Mostra os pior dois Estados, e quando paira o rato sobre o status, apresenta um Estado de rollup de todos os pods no contentor. Se não existir um estado estável, o valor de estado apresenta **(0)** . 

No Seletor de, selecione **contentores**.

![Ver os contentores selecionadas](./media/container-insights-analyze/containers-containers-tab.png)

Aqui pode ver o estado de funcionamento do desempenho de seus contentores do Kubernetes do Azure e o Azure Container Instances.  

![\<Exibição de desempenho de controladores de > de nome](./media/container-insights-analyze/containers-containers-view.png)

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

## <a name="disk-capacity-workbook"></a>Pasta de trabalho de capacidade de disco
As pastas de trabalho combinam texto, [consultas de log](../log-query/query-language.md), [métricas](../platform/data-platform-metrics.md)e parâmetros em relatórios interativos sofisticados. As pastas de trabalho são editáveis por outros membros da equipe que têm acesso aos mesmos recursos do Azure.

Azure Monitor para contêineres inclui uma pasta de trabalho para você começar, **capacidade de disco**.  Esta pasta de trabalho apresenta gráficos de uso de disco interativo para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

- % De uso do disco para todos os discos
- Espaço livre em disco para todos os discos
- Uma tabela que mostra para cada disco de nós, seu% espaço usado, tendência de% espaço usado, espaço livre em disco (GiB) e tendência de espaço livre em disco (GiB). Quando uma linha é selecionada na tabela,% espaço usado e espaço livre em disco (GiB) é mostrado abaixo 

Você acessa essa pasta de trabalho selecionando **capacidade de disco** na lista suspensa **exibir pastas de trabalho** .  

![Exibir lista suspensa de pastas de trabalho](./media/container-insights-analyze/view-workbooks-dropdown-list.png)


## <a name="next-steps"></a>Passos Seguintes
- Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps. 
- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.
