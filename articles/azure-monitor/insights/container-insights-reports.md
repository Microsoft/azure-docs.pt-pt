---
title: Relatórios em insights de contentores
description: Descreve relatórios disponíveis para analisar dados recolhidos por insights do Contentor.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: ca74521a08d4edaa498e00e6452d8f69912e4bb9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032804"
---
# <a name="reports-in-container-insights"></a>Relatórios em insights de contentores
Recomenda-se relatórios em livros de [trabalho Azure](../visualize/workbooks-overview.md)fora da caixa. Este artigo descreve os diferentes relatórios que estão disponíveis e como acessá-los.

## <a name="viewing-reports"></a>Relatórios de visualização
A partir do menu **Azure Monitor** no portal Azure, selecione **Containers**. Selecione **Insights** na secção **de Monitorização,** escolha um determinado cluster e, em seguida, selecione a página **Relatórios.** 

[![Página de relatórios](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Criar um livro personalizado
Para criar um livro personalizado baseado em qualquer um destes livros, selecione o **dropdown dos Livros de Visualização** e, em seguida, **vá para a Galeria AKS** na parte inferior do dropdown. Consulte [os livros de trabalho do Monitor Azure](../visualize/workbooks-overview.md) para obter mais informações sobre livros de trabalho e utilizando modelos de livros de trabalho.

[![Galeria AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Livros de nó

- **Capacidade do** disco : Gráficos de utilização de discos interativos para cada disco apresentado no nó dentro de um recipiente pelas seguintes perspetivas:

    - Uso por percentagem de disco para todos os discos.
    - Espaço livre em disco para todos os discos.
    - Uma grelha que mostra o disco de cada nó, a sua percentagem de espaço usado, tendência de percentagem de espaço usado, espaço em disco livre (GiB) e tendência de espaço livre em disco (GiB). Quando uma linha é selecionada na tabela, a percentagem de espaço utilizado e espaço de disco livre (GiB) é mostrada por baixo da linha.

- **Disco IO**: Gráficos de utilização de discos interativos para cada disco apresentado no nó dentro de um recipiente pelas seguintes perspetivas:

    - Disco I/O resumido em todos os discos por leitura bytes/seg, escreve bytes/seg, e lê e escreve as tendências bytes/seg.
    - Oito gráficos de desempenho mostram indicadores-chave de desempenho para ajudar a medir e identificar estrangulamentos de E/O do disco.

- **GPU**: Gráficos de utilização interativas da GPU para cada nó de cluster Kubernetes consciente da GPU.

## <a name="resource-monitoring-workbooks"></a>Livros de monitorização de recursos

- **Implementações**: Estado das suas implementações & Autoscaler de cápsulas horizontais (HPA), incluindo HPA personalizado. 
  
- **Detalhes da carga de trabalho**: Gráficos interativos que mostram estatísticas de desempenho das cargas de trabalho para um espaço de nome. Inclui vários separadores:

  - Visão geral do uso de CPU e Memória por POD.
  - Estado do POD/Contentor mostrando a tendência de reinício do POD, tendência de reinício do contentor e estado do contentor para PODs.
  - Eventos kubernetes mostrando resumo dos eventos para o controlador.

- **Kubelet**: Inclui duas grelhas que mostram estatísticas operacionais de nó-chave:

    - A visão geral por rede de nó resume o funcionamento total, erros totais e operações bem sucedidas por cento e tendência para cada nó.
    - A visão geral por tipo de operação resume para cada operação o total de operações, erros totais e operações bem sucedidas por cento e tendência.
## <a name="billing-workbooks"></a>Livros de faturação

- **Utilização de Dados**: Ajuda-o a visualizar a origem dos seus dados sem ter de construir a sua própria biblioteca de consultas a partir do que partilhamos na nossa documentação. Neste livro, existem gráficos com os quais pode ver dados faturantes de perspetivas como:

  - Total de dados faturados em GB por solução
  - Dados faturados ingeridos por registos de contentores (registos de aplicações)
  - Registos de contentores faturados dados ingeridos por Kubernetes namespace
  - Registos de contentores faturados ingeridos segregados pelo nome cluster
  - Dados de registo de contentores ressarjáveis ingeridos por entrada de logsource
  - Dados de diagnóstico faturados por registos de nó de mestre de diagnóstico

## <a name="networking-workbooks"></a>Livros de trabalho em rede

- **Configuração NPM**: Monitorização das configurações da rede que são configuradas através do gestor de políticas de rede (NPM).

  - Informação sumária sobre a complexidade geral da configuração.
  - A política, a regra e o conjunto contam ao longo do tempo, permitindo uma visão da relação entre os três e adicionando uma dimensão de tempo para depurar uma configuração.
  - Número de entradas em todos os IPSets e em cada IPSet.
  - Pior e médio desempenho de caso por nó para adicionar componentes à sua Configuração de Rede.

- **Rede**: Gráficos de utilização de rede interativos para o adaptador de rede de cada nó, e uma grelha apresenta os principais indicadores de desempenho para ajudar a medir o desempenho dos seus adaptadores de rede.



## <a name="next-steps"></a>Passos seguintes

- Consulte [os livros de trabalho do Monitor Azure](../visualize/workbooks-overview.md) para obter mais detalhes sobre os livros de trabalho no Azure Monitor.
