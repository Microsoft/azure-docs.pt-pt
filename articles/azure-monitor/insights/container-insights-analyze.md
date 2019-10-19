---
title: Monitorar o desempenho do cluster AKS com o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode exibir e analisar os dados de desempenho e de log com Azure Monitor para contêineres.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/17/2019
ms.openlocfilehash: a65951a90767acce5570244af8a9250845e12def
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554238"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Entender o desempenho do cluster AKS com Azure Monitor para contêineres
Com Azure Monitor para contêineres, você pode usar os gráficos de desempenho e o status de integridade para monitorar a carga de trabalho de seus clusters do AKS (serviço kubernetes do Azure) de duas perspectivas. Você pode monitorar diretamente de um cluster AKS ou pode monitorar todos os clusters do AKS em uma assinatura do Azure Monitor. A exibição de instâncias de contêiner do Azure também é possível quando você monitora um cluster AKS específico.

Este artigo ajuda você a entender as duas perspectivas e como Azure Monitor ajuda a avaliar, investigar e resolver problemas detectados rapidamente.

Para obter informações sobre como habilitar o Azure Monitor para contêineres, consulte [Azure monitor integrado para contêineres](container-insights-onboard.md).

Azure Monitor fornece uma exibição de vários clusters que mostra o status de integridade de todos os clusters AKS monitorados que executam o Linux e o Windows Server 2019 implantados em grupos de recursos em suas assinaturas. Ele mostra os clusters AKS descobertos que não são monitorados pela solução. Você pode entender imediatamente a integridade do cluster e, a partir daqui, pode fazer uma busca detalhada na página de desempenho do nó e do controlador ou navegar para ver os gráficos de desempenho do cluster. Para clusters AKS que foram descobertos e identificados como não monitorados, você pode habilitar o monitoramento para eles a qualquer momento. 

As principais diferenças no monitoramento de um cluster do Windows Server com Azure Monitor para contêineres em comparação com um cluster do Linux são as seguintes:

- A métrica RSS de memória não está disponível para o nó e contêineres do Windows.
- As informações de capacidade de armazenamento em disco não estão disponíveis para nós do Windows.
- O suporte a logs dinâmicos está disponível com exceção dos logs de contêiner do Windows.
- Somente ambientes de Pod são monitorados, não ambientes de Docker.
- Com a versão de visualização, há suporte para um máximo de 30 contêineres do Windows Server. Essa limitação não se aplica a contêineres do Linux. 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Exibição de vários clusters de Azure Monitor

Para exibir o status de integridade de todos os clusters do AKS implantados, selecione **Monitor** no painel esquerdo na portal do Azure. Na seção **insights** , selecione **contêineres**. 

![Exemplo de painel de vários clusters Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Na guia **clusters monitorados** , você aprende o seguinte:

- Quantos clusters estão em um estado crítico ou não íntegro, em comparação com quantas estão íntegras ou não relatando (referido como um estado desconhecido).
- Se todas as implantações do [mecanismo de kubernetes do Azure (AKs-Engine)](https://github.com/Azure/aks-engine) estão íntegras.
- Quantos nós e pods de sistema e de usuário são implantados por cluster.
- Quanto espaço em disco está disponível e se há um problema de capacidade.

Os status de integridade incluídos são: 

* **Íntegro**: nenhum problema é detectado para a VM e está funcionando conforme necessário. 
* **Crítico**: é detectado um ou mais problemas críticos que devem ser resolvidos para restaurar o estado operacional normal conforme o esperado.
* **Aviso**: um ou mais problemas detectados devem ser resolvidos ou a condição de integridade pode se tornar crítica.
* **Desconhecido**: se o serviço não fosse capaz de fazer uma conexão com o nó ou Pod, o status é alterado para um estado desconhecido.
* **Não encontrado**: o espaço de trabalho, o grupo de recursos ou a assinatura que contém o espaço de trabalho para esta solução foi excluído.
* **Não autorizado**: o usuário não tem as permissões necessárias para ler os dados no espaço de trabalho.
* **Erro**: ocorreu um erro ao tentar ler os dados do espaço de trabalho.
* **Mal**configurado: Azure monitor para contêineres não foi configurado corretamente no espaço de trabalho especificado.
* **Sem dados**: os dados não foram relatados para o espaço de trabalho nos últimos 30 minutos.

O estado de integridade calcula o status geral do cluster como o *pior dos* três Estados com uma exceção. Se qualquer um dos três Estados for desconhecido, o estado geral do cluster mostrará **desconhecido**. 

A tabela a seguir fornece uma análise do cálculo que controla os Estados de integridade de um cluster monitorado na exibição de vários clusters.

| |Estado |Disponibilidade |  
|-------|-------|-----------------|  
|**Pod do usuário**| | |  
| |Bom estado de funcionamento |100% |  
| |Aviso |90-99% |  
| |Crítico |< 90% |  
| |Desconhecido |Se não for relatado nos últimos 30 minutos |  
|**Pod do sistema**| | |  
| |Bom estado de funcionamento |100% |
| |Aviso |N/A |
| |Crítico |< 100% |
| |Desconhecido |Se não for relatado nos últimos 30 minutos |
|**Node** | | |
| |Bom estado de funcionamento |> 85% |
| |Aviso |60-84% |
| |Crítico |< 60% |
| |Desconhecido |Se não for relatado nos últimos 30 minutos |

Na lista de clusters, você pode fazer uma busca detalhada na página do **cluster** selecionando o nome do cluster. Em seguida, vá para a página de desempenho de **nós** selecionando o acúmulo de nós na coluna **nós** para esse cluster específico. Ou, você pode fazer uma busca detalhada na página de desempenho de **controladores** selecionando o acúmulo da coluna **pods do usuário** ou pods do **sistema** .  

## <a name="view-performance-directly-from-an-aks-cluster"></a>Exibir o desempenho diretamente de um cluster AKS

O acesso a Azure Monitor para contêineres está disponível diretamente de um cluster AKS selecionando **insights** no painel esquerdo. As informações sobre o cluster AKS são organizadas em quatro perspectivas:

- Cluster
- Nós 
- Controladores 
- Contentores

A página padrão é aberta quando você seleciona **insights**  > **cluster**. Quatro gráficos de desempenho de linha exibem as principais métricas de desempenho do cluster. 

![Exemplos de gráficos de desempenho na guia cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Os gráficos de desempenho exibem quatro métricas de desempenho:

- **Utilização de CPU do nó &nbsp; %** : uma perspectiva agregada da utilização da CPU para todo o cluster. Para filtrar os resultados para o intervalo de tempo, selecione **AVG**, **min**, **50 º**, **90 º**, **95 º**ou **Max** no seletor de percentils acima do gráfico. Os filtros podem ser usados individualmente ou combinados. 
- **Utilização de memória do nó &nbsp; %** : uma perspectiva agregada de utilização de memória para todo o cluster. Para filtrar os resultados para o intervalo de tempo, selecione **AVG**, **min**, **50 º**, **90 º**, **95 º**ou **Max** no seletor de percentils acima do gráfico. Os filtros podem ser usados individualmente ou combinados. 
- **Contagem de nós**: uma contagem de nós e status de kubernetes. Os status dos nós de cluster representados são total, pronto e não pronto. Eles podem ser filtrados individualmente ou combinados no seletor acima do gráfico. 
- **Contagem de Pod ativo**: uma contagem de Pod e o status de kubernetes. Os status dos pods representados são total, pendente, em execução, desconhecido, com êxito ou falha. Eles podem ser filtrados individualmente ou combinados no seletor acima do gráfico. 

Use as teclas de seta para a esquerda e para a direita para percorrer cada ponto de dados no gráfico. Use as teclas de seta para cima e para baixo para percorrer as linhas percentuais. Selecione o ícone de pino no canto superior direito de qualquer um dos gráficos para fixar o gráfico selecionado no último painel do Azure exibido. No painel, você pode redimensionar e reposicionar o gráfico. Selecionar o gráfico no painel redireciona você para Azure Monitor para contêineres e carrega o escopo e a exibição corretos.

O Azure Monitor para contêineres também dá suporte a Azure Monitor [métricas Explorer](../platform/metrics-getting-started.md), onde você pode criar seus próprios gráficos de plotagem, correlacionar e investigar tendências e fixar em painéis. No Metrics Explorer, você também pode usar os critérios definidos para visualizar suas métricas como a base de uma regra de [alerta baseada em métrica](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Exibir métricas de contêiner no Metrics Explorer

No Metrics Explorer, você pode exibir as métricas de utilização de nó e Pod agregadas de Azure Monitor para contêineres. A tabela a seguir resume os detalhes para ajudá-lo a entender como usar os gráficos de métrica para visualizar as métricas de contêiner.

|Namespace | Métrica | Descrição | 
|----------|--------|-------------|
| informações. contêiner/nós | |
| | cpuUsageMillicores | Medição agregada da utilização da CPU em todo o cluster. É um núcleo de CPU dividido em 1000 unidades (Mili = 1000). Usado para determinar o uso de núcleos em um contêiner em que muitos aplicativos podem estar usando um núcleo.| 
| | cpuUsagePercentage | Utilização média de CPU agregada medida em percentual em todo o cluster.|
| | memoryRssBytes | Memória RSS do contêiner usada em bytes.| 
| | memoryRssPercentage | Memória RSS do contêiner usada em percentual.|
| | memoryWorkingSetBytes | Memória do conjunto de trabalho do contêiner usada.| 
| | memoryWorkingSetPercentage | Memória do conjunto de trabalho do contêiner usada em porcentagem. | 
| | nodesCount | Uma contagem de nós de kubernetes.|
| percepções. Container/pods | |
| | podCount | Uma contagem de pod de kubernetes.|

Você pode [dividir](../platform/metrics-charts.md#apply-splitting-to-a-chart) uma métrica para exibi-la por dimensão e visualizar como os diferentes segmentos de ti se comparam entre si. Para um nó, você pode segmentar o gráfico pela dimensão do *host* . De um pod, você pode segmentá-lo pelas seguintes dimensões:

* Controle
* Namespace kubernetes
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analisar nós, controladores e integridade do contêiner

Quando você alterna para as guias **nós**, **controladores**e **contêineres** , um painel de propriedades é exibido automaticamente no lado direito da página. Ele mostra as propriedades do item selecionado, que inclui os rótulos que você definiu para organizar objetos kubernetes. Quando um nó do Linux é selecionado, a seção **capacidade do disco local** também mostra o espaço em disco disponível e a porcentagem usada para cada disco apresentado ao nó. Selecione o link **>>** no painel para exibir ou ocultar o painel.

![Exemplos de painéis de propriedades de perspectivas kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

À medida que você expande os objetos na hierarquia, o painel Propriedades é atualizado com base no objeto selecionado. No painel, você também pode exibir eventos kubernetes com pesquisas de log predefinidas selecionando o link **Exibir logs de eventos kubernetes** na parte superior do painel. Para obter mais informações sobre como exibir dados de log do kubernetes, consulte [logs de pesquisa para analisar dados](container-insights-log-search.md). Ao examinar os recursos de cluster, você pode ver logs de contêiner e eventos em tempo real. Para obter mais informações sobre esse recurso e a configuração necessária para conceder e controlar o acesso, consulte [Exibir logs em tempo real com Azure monitor para contêineres](container-insights-live-logs.md). 

Use a opção **+ Adicionar filtro** na parte superior da página para filtrar os resultados da exibição por **serviço**, **nó**, **namespace**ou pool de **nós**. Depois de selecionar o escopo do filtro, selecione um dos valores mostrados no campo **Selecionar valor (es)** . Depois que o filtro é configurado, ele é aplicado globalmente ao exibir qualquer perspectiva do cluster AKS. A fórmula só dá suporte ao sinal de igual. Você pode adicionar filtros adicionais na parte superior do primeiro para restringir ainda mais os resultados. Por exemplo, se você especificar um filtro por **nó**, só poderá selecionar **serviço** ou **namespace** para o segundo filtro.

![Exemplo usando o filtro para restringir os resultados](./media/container-insights-analyze/add-filter-option-01.png)

A especificação de um filtro em uma guia continua sendo aplicada quando você seleciona outra. Ele é excluído depois que você seleciona o símbolo **x** ao lado do filtro especificado. 

Alterne para a guia **nós** e a hierarquia de linhas segue o modelo de objeto kubernetes, que começa com um nó no cluster. Expanda o nó para exibir um ou mais pods em execução no nó. Se mais de um contêiner for agrupado a um pod, eles serão exibidos como a última linha na hierarquia. Você também pode exibir quantas cargas de trabalho não-Pod estão em execução no host se o host tiver pressão de memória ou processador.

![Exemplo da hierarquia do nó kubernetes no modo de exibição de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Os contêineres do Windows Server que executam o sistema operacional Windows Server 2019 são mostrados após todos os nós baseados em Linux na lista. Ao expandir um nó do Windows Server, você pode exibir um ou mais pods e contêineres que são executados no nó. Depois que um nó é selecionado, o painel Propriedades mostra informações de versão. As informações do agente são excluídas porque os nós do Windows Server não têm um agente instalado. 

![Hierarquia de nós de exemplo com nós do Windows Server listados](./media/container-insights-analyze/nodes-view-windows.png) 

Instâncias de contêiner do Azure nós virtuais que executam o sistema operacional Linux são mostradas após o último nó de cluster AKS na lista. Quando você expande um nó virtual de instâncias de contêiner, é possível exibir um pods e contêineres de instâncias de contêiner que são executados no nó. As métricas não são coletadas e relatadas para nós, somente para pods.

![Hierarquia de nós de exemplo com instâncias de contêiner listadas](./media/container-insights-analyze/nodes-view-aci.png)

Em um nó expandido, você pode fazer drill-down do Pod ou contêiner que é executado no nó para o controlador para exibir os dados de desempenho filtrados para esse controlador. Selecione o valor na coluna **controlador** para o nó específico.
 
![Exemplo de busca detalhada de nó para controlador no modo de exibição de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Selecione controladores ou contêineres na parte superior da página para examinar o status e a utilização de recursos para esses objetos. Para examinar a utilização de memória, na lista suspensa **métrica** , selecione **conjunto de trabalho** **memória RSS** ou memória. O **RSS de memória** tem suporte apenas para Kubernetes versão 1,8 e posterior. Caso contrário, você exibirá valores para **Min &nbsp; %** como *Nan &nbsp; %* , que é um valor de tipo de dados numérico que representa um valor indefinido ou inrepresentado.

![Exibição de desempenho de nós de contêiner](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Conjunto de trabalho de memória** mostra a memória residente e a memória virtual (cache) incluídas e é um total do que o aplicativo está usando. O **RSS de memória** mostra somente a memória principal (que não é nada, exceto a memória residente em outras palavras). Essa métrica mostra a capacidade real da memória disponível. Qual é a diferença entre a memória residente e a memória virtual?

- Memória residente ou memória principal, é a quantidade real de memória do computador disponível para os nós do cluster.

- A memória virtual é um espaço reservado no disco rígido (cache) usado pelo sistema operacional para trocar dados da memória em disco quando sob pressão de memória e, em seguida, busque-os de volta para a memória quando necessário.

Por padrão, os dados de desempenho são baseados nas últimas seis horas, mas você pode alterar a janela usando a opção **intervalo** de tempo no canto superior esquerdo. Você também pode filtrar os resultados dentro do intervalo de tempo selecionando **min**, **AVG**, **50 º**, **90 º**, **95 º**e **Max** no seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando você focaliza o gráfico de barras na coluna **tendência** , cada barra mostra o uso de CPU ou de memória, dependendo de qual métrica está selecionada, dentro de um período de exemplo de 15 minutos. Depois de selecionar o gráfico de tendências por meio de um teclado, use a tecla Alt + Page Up ou Alt + Page Down para percorrer cada barra individualmente. Você obterá os mesmos detalhes que faria se tiver focalizado a barra.

![Exemplo de foco de gráfico de barra de tendência](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

No próximo exemplo, para o primeiro nó na lista, *AKs-nodepool1-* , o valor para **contêineres** é 9. Esse valor é um ROLLUP do número total de contêineres implantados.

![ROLLUP de contêineres por exemplo por nó](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Essas informações podem ajudá-lo a identificar rapidamente se você tem um equilíbrio adequado de contêineres entre nós em seu cluster. 

As informações apresentadas quando você exibe a guia **nós** é descrita na tabela a seguir.

| Column | Descrição | 
|--------|-------------|
| Nome | O nome do host. |
| Estado | Kubernetes exibição do status do nó. |
| Min &nbsp;%, AVG &nbsp;%, 50 º &nbsp;%, 90 º &nbsp;%, 95 º &nbsp;%, Max &nbsp; %  | Porcentagem média do nó com base no percentil durante a duração selecionada. |
| Min, AVG, 50 º, 90 º, 95 º, Max | Valor real dos nós médios com base no percentil durante o tempo de duração selecionado. O valor médio é medido do limite de CPU/memória definido para um nó. Para pods e contêineres, é o valor médio relatado pelo host. |
| Contentores | Número de contêineres. |
| Atividade | Representa o tempo desde que um nó foi iniciado ou reinicializado. |
| Controle | Somente para contêineres e pods. Ele mostra em qual controlador ele reside. Nem todos os pods estão em um controlador, portanto, alguns podem exibir **N/a**. | 
| Trend min &nbsp;%, AVG &nbsp;%, 50 º &nbsp;%, 90 º &nbsp;%, 95 º &nbsp;%, Max &nbsp; % | A tendência do gráfico de barras representa o percentual médio de métrica de percentil do controlador. |

No seletor, selecione **controladores**.

![Selecionar exibição de controladores](./media/container-insights-analyze/containers-controllers-tab.png)

Aqui você pode exibir a integridade do desempenho de seus controladores e das instâncias de contêiner controladores de nó virtual ou pods de nó virtual não conectados a um controlador.

![exibição de desempenho de controladores de > \<Name](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia de linhas começa com um controlador. Ao expandir um controlador, você exibe um ou mais pods. Expanda um pod e a última linha exibe o contêiner agrupado para o pod. A partir de um controlador expandido, você pode fazer uma busca detalhada no nó em que ele está sendo executado para exibir os dados de desempenho filtrados para esse nó. As instâncias de contêiner pods não conectadas a um controlador são listadas por último na lista.

![Exemplo de hierarquia de controladores com as instâncias de contêiner pods listadas](./media/container-insights-analyze/controllers-view-aci.png)

Selecione o valor na coluna **nó** para o controlador específico.

![Exemplo de busca detalhada de nó para controlador no modo de exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações exibidas quando você exibe controladores são descritas na tabela a seguir.

| Column | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | O status de rollup dos contêineres após a conclusão da execução com status, como *OK*, *encerrado*, *com falha*, *parado*ou em *pausa*. Se o contêiner estiver em execução, mas o status não tiver sido exibido corretamente ou não tiver sido selecionado pelo agente e não tiver respondido por mais de 30 minutos, o status será *desconhecido*. Detalhes adicionais do ícone de status são fornecidos na tabela a seguir.|
| Min &nbsp;%, AVG &nbsp;%, 50 º &nbsp;%, 90 º &nbsp;%, 95 º &nbsp;%, Max &nbsp; %| Média de rollup do percentual médio de cada entidade para a métrica selecionada e o percentil. |
| Min, AVG, 50 º, 90 º, 95 º, Max  | ROLLUP do desempenho médio da CPU millicore ou da memória do contêiner para o percentil selecionado. O valor médio é medido do limite de CPU/memória definido para um pod. |
| Contentores | Número total de contêineres para o controlador ou Pod. |
| Reinicia | Acúmulo da contagem de reinicialização de contêineres. |
| Atividade | Representa o tempo desde que um contêiner foi iniciado. |
| Nó | Somente para contêineres e pods. Ele mostra em qual controlador ele reside. | 
| Trend min &nbsp;%, AVG &nbsp;%, 50 º &nbsp;%, 90 º &nbsp;%, 95 º &nbsp;%, Max &nbsp; % | A tendência do gráfico de barras representa a métrica média de percentil do controlador. |

Os ícones no campo status indicam o status online dos contêineres.
 
| Cone | Estado | 
|--------|-------------|
| ![Ícone de status de execução pronta](./media/container-insights-analyze/containers-ready-icon.png) | Em execução (pronto)|
| ![Ícone de status aguardando ou pausado](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Ícone de status da última execução relatado](./media/container-insights-analyze/containers-grey-icon.png) | Última reportada em execução, mas não respondida por mais de 30 minutos|
| ![Ícone de status bem-sucedido](./media/container-insights-analyze/containers-green-icon.png) | Parada com êxito ou falha ao parar|

O ícone de status exibe uma contagem com base no que o Pod fornece. Ele mostra os dois piores Estados e, quando você passa o mouse sobre o status, ele exibe um status de Rollup de todos os pods no contêiner. Se não houver um estado pronto, o valor de status será exibido **(0)** .

No seletor, selecione **contêineres**.

![Selecionar exibição de contêineres](./media/container-insights-analyze/containers-containers-tab.png)

Aqui você pode exibir a integridade do desempenho de seus contêineres do Azure kubernetes e de instâncias de contêiner do Azure. 

![exibição de desempenho de contêineres de > \<Name](./media/container-insights-analyze/containers-containers-view.png)

A partir de um contêiner, você pode fazer uma busca detalhada em um pod ou nó para exibir os dados de desempenho filtrados para esse objeto. Selecione o valor na coluna **Pod** ou **nó** para o contêiner específico.

![Exemplo de busca detalhada de nó para contêineres no modo de exibição de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações exibidas quando você exibe contêineres são descritas na tabela a seguir.

| Column | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | Status dos contêineres, se houver. Detalhes adicionais do ícone de status são fornecidos na próxima tabela.|
| Min &nbsp;%, AVG &nbsp;%, 50 º &nbsp;%, 90 º &nbsp;%, 95 º &nbsp;%, Max &nbsp; % | O acúmulo da porcentagem média de cada entidade para a métrica selecionada e o percentil. |
| Min, AVG, 50 º, 90 º, 95 º, Max | O acúmulo do desempenho médio de CPU millicore ou memória do contêiner para o percentil selecionado. O valor médio é medido do limite de CPU/memória definido para um pod. |
| Pod | Contêiner onde reside o pod.| 
| Nó |  Nó onde reside o contêiner. | 
| Reinicia | Representa o tempo desde que um contêiner foi iniciado. |
| Atividade | Representa o tempo desde que um contêiner foi iniciado ou reinicializado. |
| Trend min &nbsp;%, AVG &nbsp;%, 50 º &nbsp;%, 90 º &nbsp;%, 95 º &nbsp;%, Max &nbsp; % | A tendência do gráfico de barras representa o percentual médio de métrica de percentil do contêiner. |

Os ícones no campo status indicam os status online de pods, conforme descrito na tabela a seguir.
 
| Cone | Estado |  
|--------|-------------|  
| ![Ícone de status de execução pronta](./media/container-insights-analyze/containers-ready-icon.png) | Em execução (pronto)|  
| ![Ícone de status aguardando ou pausado](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|  
| ![Ícone de status da última execução relatado](./media/container-insights-analyze/containers-grey-icon.png) | Última reportada em execução, mas não respondida em mais de 30 minutos|  
| ![Ícone de status encerrado](./media/container-insights-analyze/containers-terminated-icon.png) | Parada com êxito ou falha ao parar|  
| ![Ícone de status com falha](./media/container-insights-analyze/containers-failed-icon.png) | Estado de falha |  

## <a name="workbooks"></a>Livros

As pastas de trabalho combinam texto, [consultas de log](../log-query/query-language.md), [métricas](../platform/data-platform-metrics.md)e parâmetros em relatórios interativos sofisticados. As pastas de trabalho são editáveis por outros membros da equipe que têm acesso aos mesmos recursos do Azure.

Azure Monitor para contêineres inclui quatro pastas de trabalho para você começar:

- **Capacidade de disco**: apresenta gráficos de uso de disco interativo para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - Percentual de uso de disco para todos os discos.
    - Espaço livre em disco para todos os discos.
    - Uma grade que mostra o disco de cada nó, sua porcentagem de espaço usado, tendência de percentual de espaço usado, espaço livre em disco (GiB) e tendência de espaço livre em disco (GiB). Quando uma linha é selecionada na tabela, a porcentagem de espaço usado e espaço livre em disco (GiB) é mostrada abaixo da linha. 

- **E/s de disco**: apresenta gráficos de utilização de disco interativo para cada disco apresentado ao nó dentro de um contêiner pelas seguintes perspectivas:

    - E/s de disco resumida em todos os discos por meio de bytes de leitura/s, bytes de gravação/s e tendências de leitura e gravação de bytes/s.
    - Oito gráficos de desempenho mostram os principais indicadores de desempenho para ajudar a medir e identificar afunilamentos de e/s de disco.

- **Kubelet**: inclui duas grades que mostram as estatísticas operacionais do nó de chave:

    - Visão geral por grade de nós resume a operação total, os erros totais e as operações bem-sucedidas por porcentagem e tendência para cada nó.
    - Visão geral por tipo de operação resume para cada operação a operação total, os erros totais e as operações bem-sucedidas por porcentagem e tendência.

- **Rede**: apresenta gráficos de utilização de rede interativa para o adaptador de rede de cada nó e uma grade apresenta os principais indicadores de desempenho para ajudar a medir o desempenho dos adaptadores de rede.

Você acessa essas pastas de trabalho selecionando cada uma na lista suspensa **exibir pastas de trabalho** .

![Exibir lista suspensa de pastas de trabalho](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Passos seguintes

- Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas para alta utilização de CPU e memória para dar suporte aos procedimentos e processos DevOps ou operacionais.
- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.
