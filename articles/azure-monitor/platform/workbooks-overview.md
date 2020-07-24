---
title: Descrição Geral dos Livros do Azure Monitor
description: Simplificar relatórios complexos com livros de trabalho parametrizados pré-construídos e personalizados
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: mbullwin
ms.openlocfilehash: 044786d6a6aa4f9f3d163090c0edfa3832023b10
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116956"
---
# <a name="azure-monitor-workbooks"></a>Livros de trabalho do Monitor Azure

Os livros fornecem uma tela flexível para análise de dados e a criação de relatórios visuais ricos dentro do portal Azure. Permitem-lhe aceder a várias fontes de dados de todo o Azure e combiná-las em experiências interativas unificadas. 

## <a name="data-sources"></a>Origens de dados

Os livros de trabalho podem consultar dados de várias fontes dentro do Azure. Os autores de livros de trabalho podem transformar estes dados para fornecer informações sobre a disponibilidade, desempenho, utilização e saúde geral dos componentes subjacentes. Por exemplo, analisar registos de desempenho de máquinas virtuais para identificar casos elevados de CPU ou de baixa memória e exibir os resultados como uma grelha num relatório interativo.
  
Mas o verdadeiro poder dos livros de trabalho é a capacidade de combinar dados de fontes diferentes num único relatório. Isto permite a criação de pontos de vista compósitos de recursos ou junta-se a recursos que permitam dados e insights mais ricos que de outra forma seriam impossíveis.

Os livros de trabalho são atualmente compatíveis com as seguintes fontes de dados:

* [Registos](workbooks-data-sources.md#logs)
* [Métricas](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Alertas (Pré-visualização)](workbooks-data-sources.md#alerts-preview)
* [Saúde da Carga de Trabalho](workbooks-data-sources.md#workload-health)
* [Azure Resource Health](workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualizações

Os livros de trabalho fornecem um rico conjunto de capacidades para visualizar os seus dados. Para exemplos detalhados de cada tipo de visualização, pode consultar os links de exemplo abaixo:

* [Texto](workbooks-visualizations.md#text)
* [Gráficos](workbooks-visualizations.md#charts)
* [Grelhas](workbooks-visualizations.md#grids)
* [Azulejos](workbooks-visualizations.md#tiles)
* [Árvores](workbooks-visualizations.md#trees)
* [Gráficos](workbooks-visualizations.md#graphs)

![Visualizações de livros de exemplo](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Introdução

Para explorar a experiência dos livros de trabalho, navegue primeiro para o serviço Azure Monitor. Isto pode ser feito digitando **o Monitor** na caixa de pesquisa no portal Azure.

Em **seguida,** selecione Livros de Trabalho .

![Screenshot do botão de livros de trabalho realçado em uma caixa vermelha](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>Galeria

Isto leva-o à galeria de livros:

![Screenshot da galeria de livros do Azure Monitor](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Livros de trabalho versus modelos de livros

Você pode ver um _livro_ em verde e uma série de _modelos_ de livro em roxo. Os modelos servem como relatórios curados que são projetados para reutilização flexível por vários utilizadores e equipas. A abertura de um modelo cria um livro transitório preenchido com o conteúdo do modelo. 

Pode ajustar os parâmetros do livro baseado no modelo e realizar análises sem medo de quebrar a futura experiência de reporte para os colegas. Se abrir um modelo, fazer alguns ajustes e, em seguida, selecionar o ícone de salvamento estará a guardar o modelo como um livro que mostraria em verde deixando o modelo original intocado. 

Sob o capot, os modelos também diferem dos livros de trabalho guardados. A poupança de um livro cria um recurso associado do Azure Resource Manager, enquanto que o livro de trabalho transitório criado ao abrir um modelo não tem nenhum recurso único associado ao mesmo. Para saber mais sobre como o controlo de acesso é gerido em livros de trabalho consulte o [artigo de controlo de acesso a livros](workbooks-access-control.md)de trabalho .

### <a name="exploring-a-workbook-template"></a>Explorando um modelo de livro

Selecione A Análise da Falha de **Aplicação** para ver um dos modelos de livro de aplicações predefinidos.

![Screenshot do modelo de análise de falha de aplicação](./media/workbooks-overview/failure-analysis.png)

Como indicado anteriormente, a abertura do modelo cria um livro temporário para que possa interagir. Por predefinição, o livro abre no modo de leitura que exibe apenas as informações para a experiência de análise pretendida que foi criada pelo autor do modelo original.

No caso deste livro em particular, a experiência é interativa. Pode ajustar a subscrição, as aplicações direcionadas e o intervalo de tempo dos dados que pretende apresentar. Uma vez feitas essas seleções, a grelha de pedidos HTTP também é interativa, pelo que a seleção de uma linha individual alterará os dados que são renderizados nos dois gráficos na parte inferior do relatório.

### <a name="editing-mode"></a>Modo de edição

Para entender como este modelo de livro é montado, você precisa trocar para o modo de edição selecionando **Edit .** 

![Screenshot do modelo de análise de falha de aplicação](./media/workbooks-overview/edit.png)

Uma vez que tenha mudado para o modo de edição, notará que algumas caixas **editar** parecem corresponder à direita correspondentes a cada aspeto individual do seu livro.

![Screenshot do botão Editar](./media/workbooks-overview/edit-mode.png)

Se selecionarmos imediatamente o botão de edição sob a grelha de dados de pedido, podemos ver que esta parte do nosso livro consiste numa consulta kusto contra dados de um recurso Application Insights.

![Screenshot da consulta subjacente de Kusto](./media/workbooks-overview/kusto.png)

Clicar nos outros botões **editar** à direita revelará uma série de componentes centrais que compõem livros como caixas de texto baseadas em [marcação, elementos](workbooks-visualizations.md#text)de [UI de seleção](workbooks-parameters.md) de parâmetros e outros [tipos de gráfico/visualização.](workbooks-visualizations.md) 

Explorar os modelos pré-construídos em modo de edição e depois modificá-los para se adaptar às suas necessidades e guardar o seu próprio livro personalizado é uma excelente maneira de começar a aprender sobre o que é possível com os livros de trabalho do Azure Monitor.

## <a name="pinning-visualizations"></a>Visualizações de fixação

Os passos de texto, consulta e métricas num livro podem ser fixados utilizando o botão pino nesses itens enquanto o livro está em modo pin, ou se o autor do livro tiver ativado as definições para que esse elemento torne visível o ícone do pino. 

Para aceder ao modo pin, clique em **Editar** para entrar no modo de edição e selecione o ícone do pino azul na barra superior. Um ícone de pino individual aparecerá então acima de cada caixa de *edição* da peça de papel correspondente no lado direito do seu ecrã.

![Experiência pin](./media/workbooks-overview/pin-experience.png)

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

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
