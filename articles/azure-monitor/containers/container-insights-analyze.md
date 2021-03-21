---
title: Monitorização de Kubernetes com insights de contentores | Microsoft Docs
description: Este artigo descreve como pode ver e analisar o desempenho de um cluster Kubernetes com insights de contentores.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 865a21e8c54d2cf569e04534fab6ec14f5519f34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124317"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-container-insights"></a>Monitorize o desempenho do cluster Kubernetes com insights de contentores

Com insights de contentores, você pode usar os gráficos de desempenho e estado de saúde para monitorizar a carga de trabalho dos clusters Kubernetes hospedados no Serviço Azure Kubernetes (AKS), Azure Stack, ou outro ambiente de duas perspetivas. Pode monitorizar diretamente a partir do cluster, ou pode ver todos os clusters numa subscrição do Azure Monitor. Visualizar instâncias do contentor Azure também é possível ao monitorizar um cluster AKS específico.

Este artigo ajuda-o a compreender as duas perspetivas, e como o Azure Monitor o ajuda a avaliar, investigar e resolver problemas detetados rapidamente.

Para obter informações sobre como permitir informações sobre o recipiente, consulte [as informações do Recipiente a Bordo](container-insights-onboard.md).

O Azure Monitor fornece uma visão multi-cluster que mostra o estado de saúde de todos os clusters kubernetes monitorizados que executam o Linux e o Windows Server 2019 implantados em grupos de recursos nas suas subscrições. Mostra aglomerados descobertos em todos os ambientes que não são monitorizados pela solução. Você pode entender imediatamente a saúde do cluster, e a partir daqui, você pode perfurar até a página de desempenho do nó e do controlador ou navegar para ver gráficos de desempenho para o cluster. Para os aglomerados AKS que foram descobertos e identificados como não monitorizados, pode permitir a monitorização a qualquer momento.

As principais diferenças na monitorização de um cluster do Windows Server com insights de contentores em comparação com um cluster Linux são descritas [aqui](container-insights-overview.md#what-does-container-insights-provide) no artigo de visão geral.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="multi-cluster-view-from-azure-monitor"></a>Vista multi-cluster do Monitor Azure

Para visualizar o estado de saúde de todos os clusters Kubernetes implantados, selecione **Monitor** a partir do painel esquerdo no portal Azure. Na secção **Insights,** selecione **Containers**.

![Exemplo do painel de instrumentos multi-cluster do Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Pode examinar os resultados apresentados na grelha para mostrar clusters que são:

* **Azure** - AKS e clusters AKS-Engine hospedados no Serviço Azure Kubernetes
* **Azure Stack (Preview)** - AKS-Engine clusters hospedados em Azure Stack
* **Non-Azure (Preview)** - Agrupamentos kubernetes hospedados no local
* **Tudo** - Veja todos os clusters Kubernetes hospedados em Azure, Azure Stack, e ambientes no local que estão a bordo de insights de contentores

Para visualizar os aglomerados de um ambiente específico, selecione-o a partir da pílula **Ambientes** no canto superior esquerdo da página.

![Exemplo de seletor de pílulas de ambiente](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

No **separador agrupamentos monitorizados,** aprende-se o seguinte:

- Quantos aglomerados estão em estado crítico ou pouco saudável, contra quantos são saudáveis ou não reportando (referido como um estado desconhecido).
- Se todas as implementações do [Motor Azure Kubernetes (motor AKS)](https://github.com/Azure/aks-engine) são saudáveis.
- Quantos nós e cápsulas de utilizador e sistema são implantados por cluster.
- Quanto espaço em disco está disponível e se há um problema de capacidade.

Os estados de saúde incluídos são:

* **Saudável**: Não são detetados problemas para o VM, e está funcionando conforme necessário.
* **Crítico:** São detetadas uma ou mais questões críticas que devem ser abordadas para restabelecer o estado operacional normal, como esperado.
* **Aviso:** São detetados um ou mais problemas que devem ser abordados ou que o estado de saúde possa tornar-se crítico.
* **Desconhecido**: Se o serviço não foi capaz de fazer uma ligação com o nó ou vagem, o estado muda para um estado desconhecido.
* **Não encontrado**: Ou o espaço de trabalho, o grupo de recursos, ou a subscrição que contém o espaço de trabalho para esta solução foi eliminado.
* **Não autorizado**: O utilizador não tem permissões necessárias para ler os dados no espaço de trabalho.
* **Erro**: Ocorreu um erro ao tentar ler dados do espaço de trabalho.
* **Configuração errada:** As introsões do recipiente não foram configuradas corretamente no espaço de trabalho especificado.
* **Não há dados:** Os dados não comunicam ao espaço de trabalho nos últimos 30 minutos.

O estado de saúde calcula o estado global do cluster como o *pior dos* três estados com uma exceção. Se algum dos três estados for desconhecido, o estado de aglomerado global mostra **desconhecido.**

O quadro seguinte fornece uma repartição do cálculo que controla os estados de saúde para um cluster monitorizado na vista multi-cluster.

| Aglomerado monitorizado |Estado |Disponibilidade |
|-------|-------|-----------------|
|**Cápsula de utilizador**| | |
| |Bom estado de funcionamento |100% |
| |Aviso |90 - 99% |
| |Crítico |<90% |
| |Desconhecido |Se não for reportado nos últimos 30 minutos |
|**Cápsula de sistema**| | |
| |Bom estado de funcionamento |100% |
| |Aviso |N/D |
| |Crítico |<100% |
| |Desconhecido |Se não for reportado nos últimos 30 minutos |
|**Nó** | | |
| |Bom estado de funcionamento |>85% |
| |Aviso |60 - 84% |
| |Crítico |<60% |
| |Desconhecido |Se não for reportado nos últimos 30 minutos |

A partir da lista de clusters, pode perfurar até à página **cluster** selecionando o nome do cluster. Em **seguida,** aceda à página de desempenho dos Nós selecionando o rollup dos nós na coluna **Nó para** esse cluster específico. Ou, pode perfurar até à página de desempenho dos **Controladores** selecionando o rollup das **cápsulas** do Utilizador ou da coluna **pods do Sistema.**

## <a name="view-performance-directly-from-a-cluster"></a>Ver desempenho diretamente de um cluster

O acesso a insights de contentores está disponível diretamente a partir de um cluster AKS **selecionando**  >  Insights **Cluster** a partir do painel esquerdo, ou quando selecionou um cluster a partir da vista multi-cluster. A informação sobre o seu cluster é organizada em quatro perspetivas:

- Cluster
- Nós
- Controladores
- Contentores

>[!NOTE]
>A experiência descrita no resto deste artigo também é aplicável para visualizar desempenho e estado de saúde dos seus clusters Kubernetes hospedados em Azure Stack ou outro ambiente quando selecionados a partir da vista multi-cluster.

A página predefinida abre e apresenta quatro gráficos de desempenho de linha que mostram métricas de desempenho chave do seu cluster.

![Gráficos de desempenho de exemplo no separador Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Os gráficos de desempenho apresentam quatro métricas de desempenho:

- **Utilização &nbsp; % do CPU do nó:** Uma perspetiva agregada da utilização do CPU para todo o cluster. Para filtrar os resultados do intervalo de tempo, selecione **Avg**, **Min**, **50th**, **90th**, **95th** ou **Max** no seletor de percenteis acima da tabela. Os filtros podem ser utilizados individualmente ou combinados.
- **Utilização da &nbsp; % memória do nó:** Uma perspetiva agregada da utilização da memória para todo o cluster. Para filtrar os resultados do intervalo de tempo, selecione **Avg**, **Min**, **50th**, **90th**, **95th** ou **Max** no seletor de percenteis acima da tabela. Os filtros podem ser utilizados individualmente ou combinados.
- **Contagem de nó:** Contagem de nó e estado de Kubernetes. Os estados dos nós de cluster representados são Total, Pronto e Não Prontos. Podem ser filtrados individualmente ou combinados no seletor acima da tabela.
- **Contagem de pods ativa:** Uma contagem de pod e estado de Kubernetes. Os estados das cápsulas representadas são total, pendente, execução, desconhecido, bem sucedido ou falhado. Podem ser filtrados individualmente ou combinados no seletor acima da tabela.

Utilize as teclas de seta esquerda e direita para pedalar através de cada ponto de dados na tabela. Utilize as teclas de seta para cima e para baixo para pedalar através das linhas percentil. Selecione o ícone pino no canto superior direito de qualquer uma das tabelas para fixar a tabela selecionada para o último painel Azure que viu. A partir do painel de instrumentos, pode redimensionar e reposicionar a tabela. A seleção do gráfico a partir do painel de instrumentos redireciona-o para insights de contentores e carrega o âmbito e a vista corretos.

Os insights do contentor também suportam [o explorador de métricas](../essentials/metrics-getting-started.md)Azure Monitor, onde pode criar os seus próprios gráficos de enredo, correlacionar e investigar tendências, e pinar para tabliers. A partir do explorador de métricas, também pode utilizar os critérios que definiu para visualizar as suas métricas como base de uma [regra de alerta baseada em métricas](../alerts/alerts-metric.md).

## <a name="view-container-metrics-in-metrics-explorer"></a>Ver métricas de recipiente no explorador de métricas

No explorador de métricas, pode ver métricas de utilização de nó agregados e pod a partir de insights de contentores. A tabela seguinte resume os detalhes para ajudá-lo a entender como usar as tabelas métricas para visualizar as métricas do recipiente.

|Espaço de Nomes | Metric | Descrição |
|----------|--------|-------------|
| insights.container/nós | |
| | cpuUsageMillicores | Medição agregada da utilização do CPU em todo o cluster. É um núcleo de CPU dividido em 1000 unidades (milli = 1000). Usado para determinar o uso de núcleos em um recipiente onde muitas aplicações podem estar usando um núcleo.|
| | cpuUsagePercentage | Utilização média agregada do CPU medida em percentagem em todo o cluster.|
| | memóriaRssBytes | Memória RSS do contentor utilizada nos bytes.|
| | memóriaSpercentage | Memória RSS do contentor usada em percentagem.|
| | memoryWorkingSetBytes | Memória do conjunto de trabalho do recipiente utilizada.|
| | memóriaWorkingSetPercentage | Memória do conjunto de trabalho do contentor usado em por cento. |
| | nodesCount | Uma contagem de nó de Kubernetes.|
| insights.contentor/pods | |
| | PodCount | Uma contagem de cápsulas de Kubernetes.|

Pode [dividir](../essentials/metrics-charts.md#apply-splitting) uma métrica para vê-la por dimensão e visualizar como diferentes segmentos se comparam uns aos outros. Para um nó, pode segmentar o gráfico pela dimensão do *anfitrião.* A partir de uma vagem, pode segmentá-lo pelas seguintes dimensões:

* Controlador
* Espaço de nomes Kubernetes
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analisar os nódes, os controladores e a saúde dos contentores

Quando muda para os **separadores De Nós,** **Controladores** e **Contentores,** um painel de propriedade aparece automaticamente no lado direito da página. Mostra as propriedades do item selecionado, que inclui as etiquetas que definiu para organizar objetos Kubernetes. Quando um nó Linux é selecionado, a secção **de capacidade do disco local** também mostra o espaço do disco disponível e a percentagem utilizada para cada disco apresentado no nó. Selecione o **>>** link no painel para visualizar ou esconder o painel.

À medida que expande os objetos na hierarquia, as propriedades desossem atualizações com base no objeto selecionado. A partir do painel, também pode ver os registos de contentores de Kubernetes (stdout/stderror), eventos e métricas de pod selecionando a ligação **de dados ao vivo (pré-visualização)** no topo do painel. Para obter mais informações sobre a configuração necessária para conceder e controlar o acesso para visualizar estes dados, consulte [Configurar os Dados Ao Vivo (pré-visualização)](container-insights-livedata-setup.md). Ao rever os recursos de cluster, pode ver estes dados do recipiente em tempo real. Para obter mais informações sobre esta funcionalidade, consulte [como visualizar registos, eventos e métricas de casulos de Kubernetes em tempo real.](container-insights-livedata-overview.md) Para visualizar os dados de registo de Kubernetes armazenados no seu espaço de trabalho com base em pesquisas de registo pré-definidas, selecione **Ver registos** de contentores da lista de drop-down **analíticos.** Para obter informações adicionais sobre este tópico, consulte [registos de pesquisa para analisar dados.](container-insights-log-search.md#search-logs-to-analyze-data)

Utilize a opção **+ Adicionar Filtro** no topo da página para filtrar os resultados da vista por **Serviço,** **Node,** **Namespace** ou **Node Pool**. Depois de selecionar o âmbito do filtro, selecione um dos valores indicados **no(s) valor(s) do campo Desemis.** Após a configuração do filtro, é aplicado globalmente enquanto visualiza qualquer perspetiva do cluster AKS. A fórmula só suporta o sinal de igualdade. Pode adicionar filtros adicionais em cima do primeiro para reduzir ainda mais os seus resultados. Por exemplo, se especificar um filtro por **Nó,** só pode selecionar **Serviço** ou **Espaço name para** o segundo filtro.

Especificar um filtro num separador continua a ser aplicado quando seleciona outro. É apagado depois de selecionar o símbolo **x** ao lado do filtro especificado.

Mude para o separador **Nodes** e a hierarquia da linha segue o modelo de objeto Kubernetes, que começa com um nó no seu cluster. Expanda o nó para ver uma ou mais cápsulas a funcionar no nó. Se mais de um contentor é agrupado numa cápsula, são exibidos como a última linha da hierarquia. Também pode ver quantas cargas de trabalho não relacionadas com o pod estão a ser executando no hospedeiro se o hospedeiro tiver pressão de processador ou memória.

![Exemplo da hierarquia do nó de Kubernetes na vista de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Os contentores do Windows Server que executam o SISTEMA Windows Server 2019 são mostrados depois de todos os nós baseados em Linux na lista. Quando expandir um nó do Servidor do Windows, pode ver uma ou mais cápsulas e recipientes que funcionam no nó. Depois de selecionado um nó, o painel de propriedades mostra informações de versão.

![Hierarquia do nó de exemplo com os nóis do Windows Server listados](./media/container-insights-analyze/nodes-view-windows.png)

Os nós virtuais do Azure Container Instances que executam o Sistema Linux SÃO apresentados após o último nó de cluster AKS na lista. Quando expandir um nó virtual de Instâncias de Contentores, pode ver uma ou mais cápsulas de contentores e contentores que funcionam no nó. As métricas não são recolhidas e reportadas para nós, apenas para cápsulas.

![Exemplo Da hierarquia do nó com instâncias de contentores listadas](./media/container-insights-analyze/nodes-view-aci.png)

A partir de um nó expandido, pode perfurar a partir da cápsula ou do recipiente que corre no nó para o controlador para ver os dados de desempenho filtrados para esse controlador. Selecione o valor sob a coluna **do Controlador** para o nó específico.

![A screenshot mostra a perfuração do nó para o controlador na vista de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Selecione controladores ou recipientes no topo da página para rever o estado e a utilização do recurso para esses objetos. Para rever a utilização da memória, na lista **de recuos métricos,** selecione **Memory RSS** ou **Memory working set**. **O Memory RSS** é suportado apenas para a versão 1.8 de Kubernetes e posterior. Caso contrário, você vê valores para **&nbsp; % Min** como *NaN &nbsp; %*, que é um valor de tipo de dados numérico que representa um valor indefinido ou não representativo.

![Vista de desempenho dos nódoas de contentores](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**O conjunto de trabalho de memória** mostra tanto a memória residente como a memória virtual (cache) incluídas e é um total do que a aplicação está a usar. **O RSS de memória** mostra apenas a memória principal (que não passa de uma memória residente em outras palavras). Esta métrica mostra a capacidade real da memória disponível. Qual é a diferença entre memória residente e memória virtual?

- Memória residente ou memória principal, é a quantidade real de memória da máquina disponível para os nós do cluster.

- A memória virtual é reservada ao espaço de disco rígido (cache) utilizado pelo sistema operativo para trocar dados da memória para o disco quando sob pressão de memória e, em seguida, reco buscá-lo de volta à memória quando necessário.

Por predefinição, os dados de desempenho baseiam-se nas últimas seis horas, mas pode alterar a janela utilizando a opção **TimeRange** no canto superior esquerdo. Também pode filtrar os resultados dentro do intervalo de tempo selecionando **Min**, **Avg**, **50º**, **90º**, **95º** e **Max** no seletor de percentil.

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando paira sobre o gráfico de barras sob a coluna **Tendência,** cada barra mostra o uso de CPU ou memória, dependendo da métrica selecionada, num período de amostragem de 15 minutos. Depois de selecionar o gráfico de tendências através de um teclado, utilize a tecla Alt+Page up ou a tecla Alt+Page para baixo para percorrer cada barra individualmente. Obtém os mesmos detalhes que conseguiria se pairasse sobre o bar.

![Exemplo de hover-over gráfico de barra de tendência](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

No exemplo seguinte, para o primeiro nó da lista, *aks-nodepool1-*, o valor para **contentores** é 9. Este valor é um rollup do número total de contentores implantados.

![Rollup de recipientes por nó exemplo](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Esta informação pode ajudá-lo a identificar rapidamente se tem um equilíbrio adequado de contentores entre nós no seu cluster.

As informações apresentadas quando vê o **separador Nodes** são descritas na tabela seguinte.

| Coluna | Descrição |
|--------|-------------|
| Nome | O nome do hospedeiro. |
| Estado | Kubernetes visualizam o estado do nó. |
| Min &nbsp; %, Avg &nbsp; %, 50º &nbsp; %, 90º &nbsp; %, 95º &nbsp; %, Máx&nbsp;%  | Percentagem média de nó com base no percentil durante a duração selecionada. |
| Min, Avg, 50º, 90º, 95º, Max | Valor real dos nós médios com base no percentil durante a duração selecionada. O valor médio é medido a partir do limite de CPU/Memória definido para um nó. Para cápsulas e contentores, é o valor médio reportado pelo hospedeiro. |
| Contentores | Número de contentores. |
| Tempo de atividade | Representa o tempo desde que um nó começou ou foi reiniciado. |
| Controlador | Apenas para contentores e cápsulas. Mostra em que controlador reside. Nem todas as cápsulas estão num controlador, por isso algumas podem apresentar **N/A**. |
| Tendência Min &nbsp; %, Avg &nbsp; %, 50º &nbsp; %, 90º &nbsp; %, 95º &nbsp; %, Máx&nbsp;% | A tendência do gráfico de barras representa a percentagem média de percentil do controlador. |

Poderá notar uma carga de trabalho depois de expandir um nó chamado **Outro processo**. Representa processos não contentorizados que funcionam no seu nó, e inclui:

* Processos não-contentorizados de Kubernetes geridos ou geridos por si próprios

* Processos de tempo de funcionamento do contentor

* Kubelet

* Processos do sistema em execução no seu nó

* Outras cargas de trabalho não-Kubernetes em execução em hardware de nó ou VM

É calculado por: *Utilização total do CAdvisor*  -  *Usage a partir de processo contentorizado*.

No seletor, selecione **Comandos**.

![Selecione a vista dos controladores](./media/container-insights-analyze/containers-controllers-tab.png)

Aqui pode ver a saúde de desempenho dos controladores e controladores de nó de instâncias de contentores ou cápsulas de nó virtuais não ligadas a um controlador.

![\<Nome> vista de desempenho dos controladores](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia da linha começa com um controlador. Quando se expande um controlador, vê-se uma ou mais cápsulas. Expanda uma vagem e a última linha exibe o recipiente agrupado à cápsula. A partir de um controlador expandido, pode perfurar até ao nó em que está a correr para ver os dados de desempenho filtrados para esse nó. As cápsulas de instâncias de contentores não ligadas a um controlador estão listadas em último lugar na lista.

![Hierarquia de controladores de exemplo com cápsulas de instâncias de contentores listadas](./media/container-insights-analyze/controllers-view-aci.png)

Selecione o valor sob a coluna **nó para** o controlador específico.

![Exemplo de perfuração do controlador para o nó na vista de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

As informações apresentadas quando visualizam os controladores são descritas na tabela seguinte.

| Coluna | Descrição |
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | O estado de rollup dos contentores depois de ter terminado a funcionar com o estado *ok,* *terminado,* *falhado,* *parado* ou *pausado*. Se o contentor estiver em funcionamento, mas o estado não foi devidamente visualizado ou não foi recolhido pelo agente e não respondeu há mais de 30 minutos, o estado é *desconhecido.* Detalhes adicionais do ícone de estado são fornecidos na tabela seguinte.|
| Min &nbsp; %, Avg &nbsp; %, 50º &nbsp; %, 90º &nbsp; %, 95º &nbsp; %, Máx&nbsp;%| Rollup média da percentagem média de cada entidade para a métrica e percentil selecionados. |
| Min, Avg, 50º, 90º, 95º, Max  | Rollup do miliére cpu médio ou desempenho de memória do recipiente para o percentil selecionado. O valor médio é medido a partir do limite cpu/memória definido para uma vagem. |
| Contentores | Número total de contentores para o controlador ou vagem. |
| Reinicia | Rollup da contagem de reinício dos contentores. |
| Tempo de atividade | Representa o tempo desde que um contentor começou. |
| Nó | Apenas para contentores e cápsulas. Mostra em que controlador reside. |
| Tendência Min &nbsp; %, Avg &nbsp; %, 50º &nbsp; %, 90º &nbsp; %, 95º &nbsp; %, Máx&nbsp;% | A tendência do gráfico de barras representa a métrica percentil média do controlador. |

Os ícones no campo de estado indicam o estado on-line dos contentores.

| Ícone | Estado |
|--------|-------------|
| ![Ícone de estado de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|
| ![Ícone de estado de espera ou pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Esperando ou Pausado|
| ![Último ícone de estado de execução relatado](./media/container-insights-analyze/containers-grey-icon.png) | Última vez que correu, mas não responde há mais de 30 minutos.|
| ![Ícone de estado de sucesso](./media/container-insights-analyze/containers-green-icon.png) | Com sucesso parou ou não conseguiu parar|

O ícone de estado apresenta uma contagem com base no que o pod fornece. Mostra os piores dois estados, e quando se paira sobre o estado, apresenta um estado de rollup de todas as cápsulas do recipiente. Se não houver um estado pronto, o valor do estado apresenta **(0)**.

No seletor, selecione **Containers**.

![Selecione a vista de contentores](./media/container-insights-analyze/containers-containers-tab.png)

Aqui pode ver a saúde de desempenho dos seus recipientes Azure Kubernetes e Azure Container Instances.

![\<Nome> visão de desempenho dos recipientes](./media/container-insights-analyze/containers-containers-view.png)

A partir de um recipiente, pode perfurar até uma vagem ou nó para ver os dados de desempenho filtrados para esse objeto. Selecione o valor sob a coluna **Pod** ou **Nó** para o recipiente específico.

![Exemplo de perfuração do nó para os contentores na vista de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

A informação apresentada quando vê os recipientes é descrita na tabela seguinte.

| Coluna | Descrição |
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | Estado dos contentores, se houver. Detalhes adicionais do ícone de estado são fornecidos na tabela seguinte.|
| Min &nbsp; %, Avg &nbsp; %, 50º &nbsp; %, 90º &nbsp; %, 95º &nbsp; %, Máx&nbsp;% | O rollup da percentagem média de cada entidade para a métrica e percentil selecionados. |
| Min, Avg, 50º, 90º, 95º, Max | O rollup do valor médio do CPU millicore ou do desempenho da memória do recipiente para o percentil selecionado. O valor médio é medido a partir do limite cpu/memória definido para uma vagem. |
| Vagem | Recipiente onde reside a cápsula.|
| Nó |  Nó onde o recipiente reside. |
| Reinicia | Representa o tempo desde que um contentor começou. |
| Tempo de atividade | Representa o tempo desde que um recipiente foi iniciado ou reiniciado. |
| Tendência Min &nbsp; %, Avg &nbsp; %, 50º &nbsp; %, 90º &nbsp; %, 95º &nbsp; %, Máx&nbsp;% | A tendência do gráfico de barras representa a percentagem média de percentil do recipiente. |

Os ícones no campo de estado indicam os estados on-line das cápsulas, conforme descrito na tabela seguinte.

| Ícone | Estado |
|--------|-------------|
| ![Ícone de estado de execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Execução (Pronto)|
| ![Ícone de estado de espera ou pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Esperando ou Pausado|
| ![Último ícone de estado de execução relatado](./media/container-insights-analyze/containers-grey-icon.png) | Última vez que correu, mas não responde há mais de 30 minutos.|
| ![Ícone de estado terminado](./media/container-insights-analyze/containers-terminated-icon.png) | Com sucesso parou ou não conseguiu parar|
| ![Ícone de estado falhado](./media/container-insights-analyze/containers-failed-icon.png) | Estado falhado |

## <a name="monitor-and-visualize-network-configurations"></a>Monitorize e visualize configurações de rede
O Azure Network Policy Manager inclui métricas prometeu informativas que permitem monitorizar e entender melhor as suas configurações de rede. Fornece visualizações incorporadas no portal Azure ou na Grafana Labs. Para mais detalhes, consulte [Configurações de Rede Monitor e Visualize com Azure NPM](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm).


## <a name="workbooks"></a>Livros

Os livros combinam texto, consultas de registo, métricas e parâmetros em relatórios interativos ricos que permitem analisar o desempenho do cluster. Consulte [os livros de trabalho em informações sobre o recipiente](../insights/container-insights-reports.md) para obter uma descrição dos livros disponíveis para informações sobre o contentor.


## <a name="next-steps"></a>Passos seguintes

- [Reveja Criar alertas de desempenho com insights](./container-insights-log-alerts.md) de Contentores para aprender a criar alertas para alta utilização de CPU e memória para suportar os seus DevOps ou processos e procedimentos operacionais.

- Consulte [exemplos de consulta de registos](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.

- Consulte [a saúde do cluster monitor](./container-insights-overview.md) para saber sobre a visualização do estado de saúde do seu cluster Kubernetes.