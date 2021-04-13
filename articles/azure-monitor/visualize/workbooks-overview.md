---
title: Descrição Geral dos Livros do Azure Monitor
description: Saiba como os livros fornecem uma tela flexível para análise de dados e a criação de relatórios visuais ricos dentro do portal Azure.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: a02e5fced0a9e338a32d8d8beaa9e4b5fca994e8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309487"
---
# <a name="azure-monitor-workbooks"></a>Livros de trabalho do Monitor Azure

Os livros fornecem uma tela flexível para a análise de dados e a criação de relatórios visuais avançados no portal do Azure. Permitem-lhe aceder a várias fontes de dados de todo o Azure e combiná-las em experiências interativas unificadas.

Aqui está um vídeo sobre a criação de livros.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Origens de dados

Os livros podem consultar dados de várias origens no Azure. Os autores de livros podem transformar estes dados para fornecer informações sobre a disponibilidade, desempenho, utilização e estado de funcionamento geral dos componentes subjacentes. Por exemplo, analisar registos de desempenho de máquinas virtuais para identificar casos elevados de CPU ou de baixa memória e exibir os resultados como uma grelha num relatório interativo.
  
Mas a verdadeira vantagem dos livros é a capacidade para combinar dados de origens diferentes num único relatório. Isto permite a criação de pontos de vista compósitos de recursos ou junta-se a recursos que permitam dados e insights mais ricos que de outra forma seriam impossíveis.

Os livros são atualmente compatíveis com as seguintes origens de dados:

* [Registos](../visualize/workbooks-data-sources.md#logs)
* [Métricas](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Alertas (Pré-visualização)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Saúde da Carga de Trabalho](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualizações

Os livros de trabalho fornecem um rico conjunto de capacidades para visualizar os seus dados. Para exemplos detalhados de cada tipo de visualização, pode consultar os links abaixo:

* [Texto](../visualize/workbooks-text-visualizations.md)
* [Gráficos](../visualize/workbooks-chart-visualizations.md)
* [Grelhas](../visualize/workbooks-grid-visualizations.md)
* [Mosaicos](../visualize/workbooks-tile-visualizations.md)
* [Árvores](../visualize/workbooks-tree-visualizations.md)
* [Gráficos](../visualize/workbooks-graph-visualizations.md)
* [Barra composta](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Exemplo de visualizações de livros" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

## <a name="getting-started"></a>Introdução

Para explorar a experiência dos livros de trabalho, navegue primeiro para o serviço Azure Monitor. Isto pode ser feito digitando **o Monitor** na caixa de pesquisa no portal Azure.

Em **seguida,** selecione Livros de Trabalho .

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Screenshot do botão de livros de trabalho realçado em uma caixa vermelha" border="false":::

### <a name="gallery"></a>Galeria

A galeria torna conveniente organizar, classificar e gerir livros de todos os tipos.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Screenshot da galeria no separador all." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Separadores de galeria

Existem quatro separadores na galeria para ajudar a organizar tipos de livros.

| Tecla de Tabulação              | Description                                       |
|------------------|---------------------------------------------------|
| Todos | Mostra os quatro principais itens para cada tipo - livros, modelos públicos e os meus modelos. Os livros de trabalho são classificados por data modificada para que você veja os oito livros de trabalho mais recentes modificados.|
| Livros | Mostra a lista de todos os livros disponíveis que criou ou é partilhado consigo. |
| Modelos públicos | Mostra a lista de todos os modelos de livro funcionais disponíveis, publicados pela Microsoft. Agrupado por categoria. |
| Meus modelos | Mostra a lista de todos os modelos de livro disponíveis que criou ou é partilhado consigo. Agrupado por categoria. |

#### <a name="features"></a>Funcionalidades

* Em cada separador, há uma grelha com informações sobre os livros. Inclui descrição, última data modificada, tags, subscrição, grupo de recursos, região e estado partilhado. Você também pode classificar os livros por esta informação.
* Filtrar por grupo de recursos, subscrições, nome de livro/modelo ou categoria de modelo.
* Selecione vários livros para eliminar ou eliminar a granel.
* Cada Livro tem um menu de contexto (elipse/três pontos no final), selecionando-o abrirá uma lista de ações rápidas.
    * Ver recurso - Aceder ao separador de recursos do livro para ver o ID de recursos do livro, adicionar tags, gerir fechaduras, etc.
    * Apague ou mude o nome do livro.
    * Pin workbook para painel.

### <a name="workbooks-versus-workbook-templates"></a>Livros de trabalho versus modelos de livros

Você pode ver um _livro_ em verde e uma série de _modelos_ de livro em roxo. Os modelos servem como relatórios curados que são projetados para reutilização flexível por vários utilizadores e equipas. A abertura de um modelo cria um livro transitório preenchido com o conteúdo do modelo.

Pode ajustar os parâmetros do livro baseado no modelo e realizar análises sem medo de quebrar a futura experiência de reporte para os colegas. Se abrir um modelo, fazer alguns ajustes e, em seguida, selecionar o ícone de salvamento estará a guardar o modelo como um livro que mostraria em verde deixando o modelo original intocado.

Sob o capot, os modelos também diferem dos livros de trabalho guardados. A poupança de um livro cria um recurso associado do Azure Resource Manager, enquanto que o livro de trabalho transitório criado ao abrir um modelo não tem nenhum recurso único associado ao mesmo. Para saber mais sobre como o controlo de acesso é gerido em livros de trabalho consulte o [artigo de controlo de acesso a livros](../visualize/workbooks-access-control.md)de trabalho .

### <a name="exploring-a-workbook-template"></a>Explorando um modelo de livro

Selecione A Análise da Falha de **Aplicação** para ver um dos modelos de livro de aplicações predefinidos.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Screenshot do modelo de análise de falha de aplicação" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Como indicado anteriormente, a abertura do modelo cria um livro temporário para que possa interagir. Por predefinição, o livro abre no modo de leitura que exibe apenas as informações para a experiência de análise pretendida que foi criada pelo autor do modelo original.

No caso deste livro em particular, a experiência é interativa. Pode ajustar a subscrição, as aplicações direcionadas e o intervalo de tempo dos dados que pretende apresentar. Uma vez feitas essas seleções, a grelha de pedidos HTTP também é interativa, pelo que a seleção de uma linha individual alterará os dados que são renderizados nos dois gráficos na parte inferior do relatório.

### <a name="editing-mode"></a>Modo de edição

Para entender como este modelo de livro é montado, você precisa trocar para o modo de edição selecionando **Edit .**

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Screenshot do botão de edição em livros de trabalho." border="false" :::

Uma vez que tenha mudado para o modo de edição, notará que algumas caixas **editar** parecem corresponder à direita correspondentes a cada aspeto individual do seu livro.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Screenshot do botão Editar" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Se selecionarmos imediatamente o botão de edição sob a grelha de dados de pedido, podemos ver que esta parte do nosso livro consiste numa consulta kusto contra dados de um recurso Application Insights.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Screenshot da consulta subjacente de Kusto" border="false" lightbox="./media/workbooks-overview/kusto.png":::


Clicar nos outros botões **editar** à direita revelará uma série de componentes centrais que compõem livros como caixas de texto baseadas em [marcação, elementos](../visualize/workbooks-text-visualizations.md)de [UI de seleção](../visualize/workbooks-parameters.md) de parâmetros e outros [tipos de gráfico/visualização.](#visualizations)

Explorar os modelos pré-construídos em modo de edição e depois modificá-los para se adaptar às suas necessidades e guardar o seu próprio livro personalizado é uma excelente maneira de começar a aprender sobre o que é possível com os livros de trabalho do Azure Monitor.

## <a name="pinning-visualizations"></a>Visualizações de fixação

Os passos de texto, consulta e métricas num livro podem ser fixados utilizando o botão pino nesses itens enquanto o livro está em modo pin, ou se o autor do livro tiver ativado as definições para que esse elemento torne visível o ícone do pino.

Para aceder ao modo pin, clique em **Editar** para entrar no modo de edição e selecione o ícone do pino azul na barra superior. Um ícone de pino individual aparecerá então acima de cada caixa de *edição* da peça de papel correspondente no lado direito do seu ecrã.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Screenshot da experiência do pino." border="false":::

> [!NOTE]
> O estado do livro é guardado no momento do pino, e os livros fixos num painel de instrumentos não serão atualizados se o livro subjacente for modificado. Para atualizar uma peça de livro fixada, terá de eliminar e voltar a fixar essa peça.

## <a name="dashboard-time-ranges"></a>Intervalos de tempo do painel

As peças de consulta de livro fixado respeitarão o intervalo de tempo do painel de instrumentos se o item fixado estiver configurado para utilizar um parâmetro *de intervalo de tempo.* O valor do intervalo de tempo do painel será utilizado como valor do parâmetro do intervalo de tempo, e qualquer alteração do intervalo de tempo do painel fará com que o item fixado seja atualizado. Se uma peça fixa estiver a utilizar o intervalo de tempo do painel de instrumentos, verá o subtítulo da atualização da peça fixada para mostrar o intervalo de tempo do painel de instrumentos sempre que o intervalo de tempo for alterativo.

Além disso, as peças de livro fixas utilizando um parâmetro de intervalo de tempo irão refrescar-se automaticamente a uma taxa determinada pelo intervalo de tempo do painel de instrumentos. A última vez que a consulta correu aparecerá na legenda da peça fixa.

Se um passo fixado tiver um intervalo de tempo explicitamente definido (não utiliza um parâmetro de intervalo de tempo), esse intervalo de tempo será sempre utilizado para o painel de instrumentos, independentemente das definições do painel de instrumentos. O subtítulo da parte fixa não mostra o intervalo de tempo do painel de instrumentos e a consulta não se atualizará automaticamente no painel de instrumentos. O subtítulo mostrará a última vez que a consulta foi executada.

> [!NOTE]
> As consultas que utilizam a fonte de dados *de fusão* não são atualmente suportadas quando se fixa aos dashboards.

## <a name="sharing-workbook-templates"></a>Partilhar modelos de livro

Assim que começar a criar os seus próprios modelos de livro, talvez queira partilhá-lo com a comunidade em geral. Para saber mais, e para explorar outros modelos que não fazem parte da vista padrão da galeria Azure Monitor visite o nosso [repositório GitHub.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) Para navegar nos livros existentes, visite a [biblioteca do livro](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) de trabalho no GitHub.

## <a name="next-step"></a>Passo seguinte

* [Começar a](#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](../visualize/workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.