---
title: Monitorização de Kubernetes com Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como pode ver e analisar o desempenho de um cluster Kubernetes com o Monitor Azure para contentores.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: f57f8982b2aa045156e6f48316610137260d6597
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385535"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Monitorize o seu desempenho do cluster Kubernetes com o Monitor Azure para contentores

Com o Monitor Azure para contentores, pode utilizar as tabelas de desempenho e o estado de saúde para monitorizar a carga de trabalho dos clusters Kubernetes hospedados no Serviço Azure Kubernetes (AKS), Azure Stack ou outro ambiente de duas perspetivas. Pode monitorizar diretamente a partir do cluster, ou pode ver todos os clusters numa subscrição do Azure Monitor. Visualizar as instâncias de contentores Azure também é possível ao monitorizar um cluster AKS específico.

Este artigo ajuda-o a compreender as duas perspetivas, e como o Azure Monitor o ajuda a avaliar, investigar e resolver rapidamente os problemas detetados.

Para obter informações sobre como ativar o Monitor Azure para contentores, consulte o [Monitor De Bordo Azure para obter recipientes](container-insights-onboard.md).

O Azure Monitor oferece uma visão multi-cluster que mostra o estado de saúde de todos os clusters Kubernetes monitorizados que executam o Linux e o Windows Server 2019 implantados em grupos de recursos nas suas subscrições. Mostra aglomerados descobertos em todos os ambientes que não são monitorizados pela solução. Você pode entender imediatamente a saúde do cluster, e a partir daqui, você pode perfurar até a página de desempenho do nó e do controlador ou navegar para ver gráficos de desempenho para o cluster. Para os aglomerados AKS que foram descobertos e identificados como não monitorizados, pode permitir a monitorização para eles a qualquer momento. 

As principais diferenças na monitorização de um cluster do Windows Server com o Monitor Azure para contentores em comparação com um cluster Linux são descritas [aqui](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) no artigo de visão geral.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Vista de cluster multi do Azure Monitor

Para ver o estado de saúde de todos os clusters Kubernetes implantados, selecione **Monitor** a partir do painel esquerdo no portal Azure. Na secção **Insights,** selecione **Recipientes**. 

![Exemplo de dashboard de cluster multi de Monitor do Azure](./media/container-insights-analyze/azmon-containers-multiview.png)

Pode ver os resultados apresentados na grelha para mostrar clusters que são:

* **Clusters Azure** - AKS e AKS-Engine hospedados no Serviço Azure Kubernetes
* **Azure Stack (Pré-visualização)** - Aglomerados AKS-Motor hospedados em Azure Stack
* **Clusters não-Azure (Pré-visualização)** - Aglomerados kubernetes alojados no local
* **Todos** - Veja todos os clusters Kubernetes alojados em ambientes Azure, Azure Stack e no local que estão a bordo do Azure Monitor para contentores

Para ver os aglomerados de um ambiente específico, selecione-o a partir da pílula **Ambientes** no canto superior esquerdo da página.

![Exemplo de seletor de pílulas de ambiente](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

No separador de **clusters monitorizados,** aprende-se o seguinte:

- Quantos aglomerados estão em estado crítico ou pouco saudável, contra quantos são saudáveis ou não reportando (referido como um estado desconhecido).
- Se todas as implementações do [Motor Azure Kubernetes (motor AKS)](https://github.com/Azure/aks-engine) são saudáveis.
- Quantos nós e cápsulas de utilizador e sistema são implantados por cluster.
- Quanto espaço em disco está disponível e se há um problema de capacidade.

Os Estados de estado de funcionamento incluídos são: 

* **Saudável**: Não são detetados problemas para o VM, e está a funcionar conforme necessário. 
* **Crítico**: São detetadas uma ou mais questões críticas que devem ser abordadas para restabelecer o estado operacional normal, como esperado.
* **Aviso**: São detetadas uma ou mais questões que devem ser abordadas ou o estado de saúde pode tornar-se crítico.
* **Desconhecido**: Se o serviço não foi capaz de fazer uma ligação com o nó ou pod, o estado muda para um estado desconhecido.
* **Não encontrado**: Ou o espaço de trabalho, o grupo de recursos ou a subscrição que contém o espaço de trabalho para esta solução foi eliminado.
* **Não autorizado**: O utilizador não necessita de permissões para ler os dados no espaço de trabalho.
* **Erro**: Ocorreu um erro ao tentar ler dados do espaço de trabalho.
* **Mal configurado:** O Monitor Azure para recipientes não foi configurado corretamente no espaço de trabalho especificado.
* **Sem dados**: Os dados não comunicam ao espaço de trabalho nos últimos 30 minutos.

O estado de saúde calcula o estado global do cluster como o *pior dos* três estados com uma exceção. Se algum dos três estados é desconhecido, o estado de aglomerado geral mostra **Desconhecido.** 

O quadro seguinte fornece uma repartição do cálculo que controla os estados de saúde para um cluster monitorizado na visão de vários clusters.

| |Estado |Disponibilidade |  
|-------|-------|-----------------|  
|**Pod de utilizador**| | |  
| |Bom estado de funcionamento |100% |  
| |Aviso |90 - 99% |  
| |Crítica |< 90% |  
| |Desconhecido |Se não comunicadas nos últimos 30 minutos |  
|**Pod do sistema**| | |  
| |Bom estado de funcionamento |100% |
| |Aviso |N/D |
| |Crítica |< 100% |
| |Desconhecido |Se não comunicadas nos últimos 30 minutos |
|**Node** | | |
| |Bom estado de funcionamento |> 85% |
| |Aviso |60 - 84% |
| |Crítica |< 60% |
| |Desconhecido |Se não comunicadas nos últimos 30 minutos |

A partir da lista de clusters, pode perfurar até a página **cluster** selecionando o nome do cluster. Em seguida, vá para a página de desempenho de **Nós** selecionando o rollup de nós na coluna **Nós** para esse cluster específico. Ou, pode perfurar até a página de desempenho dos **Controladores** selecionando o rollup das **cápsulas utilizador** ou da coluna de **cápsulas do Sistema.**

## <a name="view-performance-directly-from-a-cluster"></a>Ver desempenho diretamente de um cluster

O acesso ao Monitor Azure para contentores está disponível diretamente a partir de um cluster AKS selecionando **Insights** > **Cluster** a partir do painel esquerdo, ou quando selecionou um cluster a partir da vista multi-cluster. A informação sobre o seu cluster é organizada em quatro perspetivas:

- Cluster
- Nós 
- Controladores 
- Contentores

>[!NOTE]
>A experiência descrita no restante deste artigo também é aplicável para visualizar o desempenho e o estado de saúde dos seus clusters Kubernetes hospedados em Azure Stack ou outro ambiente quando selecionados a partir da vista multi-cluster. 

A página predefinida abre e exibe quatro gráficos de desempenho de linha que mostram as métricas de desempenho chave do seu cluster. 

![Exemplos de gráficos de desempenho no separador de Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Os gráficos de desempenho apresentam quatro métricas de desempenho:

- Utilização do **CPU do nó&nbsp;%** : Uma perspetiva agregada da utilização do CPU para todo o cluster. Para filtrar os resultados para a faixa horcada, selecione **Avg,** **Min,** **50º,** **90º,** **95º,** ou **Max** no seletor de percentículos acima da tabela. Os filtros podem ser utilizados individualmente ou combinados. 
- **Utilização da memória do nó&nbsp;%:** Uma perspetiva agregada de utilização da memória para todo o cluster. Para filtrar os resultados para a faixa horcada, selecione **Avg,** **Min,** **50º,** **90º,** **95º,** ou **Max** no seletor de percentículos acima da tabela. Os filtros podem ser utilizados individualmente ou combinados. 
- **Contagem do nó**: Uma contagem de nó e estado de Kubernetes. Os estados dos nós de cluster representados são Total, Ready e Not Ready. Podem ser filtrados individualmente ou combinados no seletor acima do gráfico. 
- **Contagem ativa de cápsulas**: Uma contagem de cápsulas e estado de Kubernetes. Os estados das cápsulas representadas são Total, Pendente, Execução, Desconhecido, Bem Sucedido ou Falhado. Podem ser filtrados individualmente ou combinados no seletor acima do gráfico. 

Utilize as teclas de seta esquerda e direita para percorrer cada ponto de dados na tabela. Utilize as teclas de seta para cima e para baixo para percorrer as linhas percentil. Selecione o ícone do pino no canto superior direito de qualquer uma das tabelas para fixar o gráfico selecionado ao último painel de instrumentos Azure que viu. A partir do tablier, pode redimensionar e reposicionar o gráfico. A seleção do gráfico do painel redireciona-o para o Monitor Azure para obter contentores e carrega o âmbito e a vista corretos.

O Azure Monitor para contentores também suporta o explorador de [métricas](../platform/metrics-getting-started.md)Do Monitor Azure, onde pode criar os seus próprios gráficos de enredo, correlacionar e investigar tendências, e pin a dashboards. A partir do explorador de métricas, também pode usar os critérios que definiu para visualizar as suas métricas como base de uma [regra de alerta baseada em métricas.](../platform/alerts-metric.md) 

## <a name="view-container-metrics-in-metrics-explorer"></a>Ver métricas de contentores no explorador de métricas

No explorador de métricas, pode ver métricas de utilização de nó agregados e cápsulas do Monitor Azure para contentores. A tabela que se segue resume os detalhes para ajudá-lo a entender como usar os gráficos métricos para visualizar as métricas do recipiente.

|Espaço de Nomes | Métrica | Descrição | 
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | Medição agregada da utilização do CPU em todo o cluster. É um núcleo cpu dividido em 1000 unidades (mili = 1000). Usado para determinar o uso de núcleos num recipiente onde muitas aplicações podem estar usando um núcleo.| 
| | cpuUsagePercentage | Utilização média agregada de CPU medida em percentagem em todo o cluster.|
| | memoryRssBytes | Memória RSS do recipiente utilizada em bytes.| 
| | memoryRssPercentage | Memória RSS do recipiente usada em percentagem.|
| | memoryWorkingSetBytes | Memória de conjunto de trabalho do recipiente utilizada.| 
| | memoryWorkingSetPercentage | Memória de conjunto de recipientes usada em percentagem. | 
| | nodesCount | Uma contagem de nó de Kubernetes.|
| insights.container/pods | |
| | PodCount | Uma contagem de cápsulas de Kubernetes.|

Pode [dividir](../platform/metrics-charts.md#apply-splitting-to-a-chart) uma métrica para vê-la por dimensão e visualizar como diferentes segmentos dela se comparam uns aos outros. Para um nó, pode segmentar o gráfico pela dimensão do *hospedeiro.* A partir de uma cápsula, pode segmentá-lo pelas seguintes dimensões:

* Controlador
* Espaço de nome kubernetes
* Nó
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analise nós, controladores e saúde de contentores

Quando muda para os **separadores Nódosos,** **Controladores**e **Recipientes,** um painel de propriedade exibe automaticamente no lado direito da página. Mostra as propriedades do item selecionado, que inclui as etiquetas que definiu para organizar objetos Kubernetes. Quando um nó Linux é selecionado, a secção capacidade do **disco local** também mostra o espaço de disco disponível e a percentagem utilizada para cada disco apresentado ao nó. Selecione **o**>>link no painel para visualizar ou esconder o painel.

À medida que expande os objetos da hierarquia, as atualizações do painel de propriedades com base no objeto selecionado. A partir do painel, também pode ver os registos de contentores Kubernetes (stdout/stderror), eventos e métricas de pod, selecionando a ligação **de dados ao vivo (pré-visualização)** view na parte superior do painel. Para obter mais informações sobre a configuração necessária para conceder e controlar o acesso à visualização destes dados, consulte [Configurar os Dados Ao Vivo (pré-visualização)](container-insights-livedata-setup.md). Enquanto analisa os recursos do cluster, pode ver estes dados a partir do contentor em tempo real. Para mais informações sobre esta funcionalidade, consulte [como ver registos, eventos e métricas de cápsulas kubernetes em tempo real](container-insights-livedata-overview.md). Para ver os dados de registo kubernetes armazenados no seu espaço de trabalho com base em pesquisas de registo pré-definidas, selecione **Ver registos** de contentores a partir da lista de drop-down **da Análise.** Para mais informações sobre este tópico, consulte [registos de pesquisa para analisar dados](container-insights-log-search.md#search-logs-to-analyze-data).

Utilize a opção **+ Adicionar filtro** na parte superior da página para filtrar os resultados para a visualização por **Serviço,** **Nó,** **Espaço de Nomeou**Piscina de **Nó**. Depois de selecionar o âmbito do filtro, selecione um dos valores mostrados no campo **Select./s.** Depois de configurado o filtro, é aplicado globalmente enquanto vê qualquer perspetiva do cluster AKS. A fórmula só suporta o sinal de igual. Pode adicionar filtros adicionais sobre aquela primeira para refinar ainda mais os resultados. Por exemplo, se especificar um filtro por **Nó,** só pode selecionar o **Espaço de Serviço** ou **Nome** para o segundo filtro.

Especificar um filtro num separador continua a ser aplicado quando seleciona outro. É apagado depois de selecionar o símbolo **x** ao lado do filtro especificado. 

Mude para o separador **Nóso** e a hierarquia da linha segue o modelo de objetokubernetes, que começa com um nó no seu cluster. Expanda o nó para ver uma ou mais cápsulas a correr no nó. Se mais de um contentor for agrupado a uma cápsula, são apresentados como a última fila da hierarquia. Também pode ver quantas cargas de trabalho não relacionadas com o pod estão a ser recorridas no hospedeiro se o hospedeiro tiver processador ou pressão de memória.

![Exemplo da hierarquia do Nó kubernetes na visão de desempenho](./media/container-insights-analyze/containers-nodes-view.png)

Os recipientes do Windows Server que executam o Sistema operativo Windows Server 2019 SÃO mostrados depois de todos os nós baseados em Linux na lista. Ao expandir um nó do Windows Server, pode ver uma ou mais cápsulas e recipientes que funcionam no nó. Depois de um nó ser selecionado, o painel de propriedades mostra informações da versão. A informação do agente está excluída porque os nós do Windows Server não têm um agente instalado. 

![Hierarquia do nó exemplo com nós do Windows Server listados](./media/container-insights-analyze/nodes-view-windows.png) 

Os nós virtuais do Contentor Azure que executam o Sistema Operativo Linux são mostrados após o último nó de cluster AKS na lista. Ao expandir um nó virtual de instâncias de contentores, pode ver um ou mais contentores E contentores que funcionam no nó. As métricas não são recolhidas e reportadas para nós, apenas para cápsulas.

![Exemplo de hierarquia de nó com o Container Instances listados](./media/container-insights-analyze/nodes-view-aci.png)

A partir de um nó expandido, pode perfurar a partir da cápsula ou do recipiente que corre no nó até ao controlador para ver os dados de desempenho filtrados para esse controlador. Selecione o valor sob a coluna **controladora** para o nó específico.
 
![Exemplo de perfuração do nó ao controlador na vista de desempenho](./media/container-insights-analyze/drill-down-node-controller.png)

Selecione controladores ou recipientes na parte superior da página para rever o estado e a utilização de recursos para esses objetos. Para rever a utilização da memória, na lista de drop-down **métrica,** selecione **Memory RSS** ou **Memory working set**. **Memory RSS** é suportado apenas para kubernetes versão 1.8 e posterior. Caso contrário, vê valores para **Min&nbsp;%** como *NaN&nbsp;%* , que é um valor numérico do tipo de dados que representa um valor indefinido ou inrepresentável.

![Vista de desempenho de nós de contentor](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**O conjunto** de trabalho de memória mostra tanto a memória residente como a memória virtual (cache) incluídas e é um total do que a aplicação está a usar. **Memory RSS** mostra apenas a memória principal (que não passa de memória residente em outras palavras). Esta métrica mostra a capacidade real da memória disponível. Qual é a diferença entre memória residente e memória virtual?

- Memória residente ou memória principal, é a quantidade real de memória da máquina disponível para os nós do cluster.

- A memória virtual é reservada ao espaço do disco rígido (cache) utilizado pelo sistema operativo para trocar dados da memória para o disco quando sob pressão de memória e, em seguida, reapá-lo de volta à memória quando necessário.

Por predefinição, os dados de desempenho baseiam-se nas últimas seis horas, mas pode alterar a janela utilizando a opção **TimeRange** na parte superior esquerda. Também pode filtrar os resultados dentro do intervalo de tempo selecionando **Min,** **Avg,** **50º,** **90º,** **95º**e **Max** no seletor de percentil. 

![Seleção de percentil para filtragem de dados](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando paira sobre o gráfico da barra sob a coluna **Trend,** cada barra mostra cpU ou uso de memória, dependendo da métrica selecionada, dentro de um período de amostra de 15 minutos. Depois de selecionar o gráfico de tendências através de um teclado, utilize a tecla Alt+Page up ou a tecla Alt+Page para o ciclo através de cada barra individualmente. Obtém os mesmos detalhes que teria se pairasse sobre o bar.

![Exemplo de hover-over gráfico de barras de tendência](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

No exemplo seguinte, para o primeiro nó da lista, *aks-nodepool1-* o valor para **Contentores** é 9. Este valor é um rollup do número total de contentores implantados.

![Rollup de recipientes por exemplo](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Estas informações podem ajudá-lo a identificar rapidamente se tem um equilíbrio adequado entre os nós do seu cluster. 

A informação que é apresentada quando vê o separador **Nós** é descrita na tabela seguinte.

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do anfitrião. |
| Estado | Vista de Kubernetes do Estado de nó. |
| Min&nbsp;%, Avg&nbsp;%, 50º&nbsp;%, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;%  | Média de percentagem de nó com base no percentil durante o período selecionado. |
| Min, Avg, 50th, 90th, 95th, Max | Valor real médio dos nódosos com base no percentil durante a duração do tempo selecionado. O valor médio é medido a partir do limite de CPU/Memória definido para um nó. Para cápsulas e contentores, é o valor médio reportado pelo hospedeiro. |
| Contentores | Número de contentores. |
| Tempo de atividade | Representa o tempo, uma vez que um nó iniciado ou foi reiniciado. |
| Controlador | Apenas para os contentores e pods. Mostra em que controlador reside. Nem todas as cápsulas estão num controlador, por isso alguns podem exibir **N/A**. | 
| Tendência Min&nbsp;%, Avg&nbsp;%, 50º&nbsp;%, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;% | Tendência de gráfico de barras representa a percentagem de métrica de percentil média do controlador. |

No seletor, selecione **Controladores**.

![Selecione vista de controladores](./media/container-insights-analyze/containers-controllers-tab.png)

Aqui pode ver a saúde de desempenho dos seus controladores e controladores de nó virtual de instâncias de contentores ou cápsulas de nó virtuais não ligadas a um controlador.

![\<Name> visão de desempenho dos controladores](./media/container-insights-analyze/containers-controllers-view.png)

A hierarquia da linha começa com um controlador. Quando expande um controlador, vê uma ou mais cápsulas. Expanda uma cápsula e a última linha exibe o contentor agrupado na cápsula. A partir de um controlador expandido, pode perfurar até ao nó que está a ser escorrendo para ver os dados de desempenho filtrados para esse nó. As cápsulas de instâncias de contentores não ligadas a um controlador estão listadas em último lugar na lista.

![Hierarquia de controladores de exemplo com pods de instâncias de contentor listados](./media/container-insights-analyze/controllers-view-aci.png)

Selecione o valor sob a coluna **Nó** para o controlador específico.

![Exemplo de perfuração do nó ao controlador na vista de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

A informação que é exibida quando vê os controladores é descrita na tabela seguinte.

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | O estado de rollup dos contentores depois determinar de funcionar com estatuto sinuoso, *terminado,* *falhado,* *parado*ou *pausado.* Se o contentor estiver em funcionamento, mas o estado não foi devidamente exibido ou não foi captado pelo agente e não respondeu há mais de 30 minutos, o estado é *Desconhecido.* Detalhes adicionais do ícone de estado são fornecidos na tabela seguinte.|
| Min&nbsp;%, Avg&nbsp;%, 50º&nbsp;%, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;%| Média de rollup da percentagem média de cada entidade para a métrica selecionada e percentil. |
| Min, Avg, 50th, 90th, 95th, Max  | Agregação do média da CPU millicore ou memória desempenho do contentor para o percentil selecionado. O valor médio é medido desde o limite de CPU/memória definido para um pod. |
| Contentores | Número total de contentores para o controlador ou pod. |
| Reinicia | Agregação da contagem de reinício de contentores. |
| Tempo de atividade | Representa o tempo desde o início de um contentor. |
| Nó | Apenas para os contentores e pods. Mostra em que controlador reside. | 
| Tendência Min&nbsp;%, Avg&nbsp;%, 50º&nbsp;%, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;% | Tendência de gráfico de barras representa a métrica de percentil média do controlador. |

Os ícones no campo de estado indicam o estado on-line dos contentores.
 
| Ícone | Estado | 
|--------|-------------|
| ![Ícone de estado em execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Em execução (pronto)|
| ![Ícone de estado de espera ou pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|
| ![Reportou pela última vez com o ícone de estado](./media/container-insights-analyze/containers-grey-icon.png) | Última vez reportado correr, mas não respondeu por mais de 30 minutos|
| ![Ícone de estado com êxito](./media/container-insights-analyze/containers-green-icon.png) | Parado ou falha ao parar com êxito|

O ícone de estado apresenta uma contagem com base no que fornece o pod. Mostra os pior dois Estados, e quando paira o rato sobre o status, apresenta um Estado de rollup de todos os pods no contentor. Se não houver um estado pronto, o valor do estado aparece **(0)** .

No seletor, selecione **Recipientes**.

![Selecione vista de contentores](./media/container-insights-analyze/containers-containers-tab.png)

Aqui pode ver o estado de funcionamento do desempenho de seus contentores do Kubernetes do Azure e o Azure Container Instances. 

![\<Name> vista de desempenho dos contentores](./media/container-insights-analyze/containers-containers-view.png)

De um contentor, pode desagregar para um pod ou o nó para ver os dados de desempenho filtrados para esse objeto. Selecione o valor sob a coluna **Pod** ou **Nó** para o recipiente específico.

![Exemplo de perfuração do nó para os recipientes na vista de desempenho](./media/container-insights-analyze/drill-down-controller-node.png)

A informação que é exibida quando vê os recipientes é descrita na tabela seguinte.

| Coluna | Descrição | 
|--------|-------------|
| Nome | O nome do controlador.|
| Estado | Estado dos contentores, se aplicável. São fornecidos detalhes adicionais do ícone de estado na tabela seguinte.|
| Min&nbsp;%, Avg&nbsp;%, 50º&nbsp;%, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;% | O rollup da percentagem média de cada entidade para a métrica selecionada e percentil. |
| Min, Avg, 50th, 90th, 95th, Max | O rollup do média da CPU millicore ou memória desempenho do contentor para o percentil selecionado. O valor médio é medido desde o limite de CPU/memória definido para um pod. |
| Pod | Contentor onde reside o pod.| 
| Nó |  Nó onde reside o contentor. | 
| Reinicia | Representa o tempo desde o início de um contentor. |
| Tempo de atividade | Representa o tempo, uma vez que um contentor foi iniciado ou reiniciado. |
| Tendência Min&nbsp;%, Avg&nbsp;%, 50º&nbsp;%, 90º&nbsp;%, 95º&nbsp;%, Max&nbsp;% | Tendência de gráfico de barras representa a percentagem de métrica de percentil média do contentor. |

Os ícones no campo de estado indicam os estados on-line das cápsulas, conforme descrito na tabela seguinte.
 
| Ícone | Estado |  
|--------|-------------|  
| ![Ícone de estado em execução pronto](./media/container-insights-analyze/containers-ready-icon.png) | Em execução (pronto)|  
| ![Ícone de estado de espera ou pausa](./media/container-insights-analyze/containers-waiting-icon.png) | Aguardando ou em pausa|  
| ![Reportou pela última vez com o ícone de estado](./media/container-insights-analyze/containers-grey-icon.png) | Por fim comunicados em execução, mas ainda não responderam em mais de 30 minutos|  
| ![Ícone de estado terminada](./media/container-insights-analyze/containers-terminated-icon.png) | Parado ou falha ao parar com êxito|  
| ![Ícone de estado com falhas](./media/container-insights-analyze/containers-failed-icon.png) | Estado de falha |  

## <a name="workbooks"></a>Livros

Os livros de trabalho combinam texto, consultas de [registo,](../log-query/query-language.md)métricas e [parâmetros](../platform/data-platform-metrics.md)em relatórios interativos ricos. Os livros de recção são editáveis por quaisquer outros membros da equipa que tenham acesso aos mesmos recursos Do Azure.

O Monitor Azure para contentores inclui quatro livros de trabalho para começar:

- **Capacidade do disco**: Apresenta gráficos de utilização interativos do disco para cada disco apresentado ao nó dentro de um recipiente pelas seguintes perspetivas:

    - Utilização por cento do disco para todos os discos.
    - Espaço de disco gratuito para todos os discos.
    - Uma grelha que mostra o disco de cada nó, a sua percentagem de espaço usado, a tendência de percentagem de espaço usado, o espaço de disco livre (GiB) e a tendência do espaço de disco livre (GiB). Quando uma linha é selecionada na tabela, a percentagem de espaço usado e espaço de disco livre (GiB) é mostrada por baixo da linha. 

- **Disco IO**: Apresenta gráficos de utilização interativo de disco para cada disco apresentado ao nó dentro de um recipiente pelas seguintes perspetivas:

    - Disco I/O resumido em todos os discos por bytes/sede, escreve bytes/seg, e lê e escreve bytes/sec tendências.
    - Oito gráficos de desempenho mostram indicadores-chave de desempenho para ajudar a medir e identificar estrangulamentos em I/S do disco.

- **Kubelet**: Inclui duas grelhas que mostram estatísticas operacionais chave do nó:

    - A visão geral por grelha de nó resume o funcionamento total, os erros totais e as operações bem sucedidas por cento e a tendência para cada nó.
    - Visão geral por tipo de operação resume para cada operação o funcionamento total, erros totais e operações bem sucedidas por cento e tendência.

- **Rede**: Apresenta gráficos de utilização de rede interativos para o adaptador de rede de cada nó, e uma grelha apresenta os principais indicadores de desempenho para ajudar a medir o desempenho dos seus adaptadores de rede.

Acede a estes livros selecionando cada um da lista de abandono seletiva da **View Workbooks.**

![Ver lista de drop-down dos livros de trabalho](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Passos seguintes

- Revê Criar alertas de desempenho com o [Monitor Azure para que](container-insights-alerts.md) os recipientes aprendam a criar alertas para alta CPU e utilização de memória para suportar os seus DevOps ou processos e procedimentos operacionais.

- Veja [exemplos](container-insights-log-search.md#search-logs-to-analyze-data) de consultas de registo para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.

- Veja a [saúde](container-insights-health.md) do cluster monitor para aprender sobre ver o estado de saúde do seu cluster Kubernetes.
