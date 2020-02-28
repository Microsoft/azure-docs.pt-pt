---
title: Descrição Geral dos Livros do Azure Monitor
description: Simplificar relatórios complexos com livros pré-construídos e parametrizados personalizados
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658239"
---
# <a name="azure-monitor-workbooks"></a>Livros de Monitorização Azure

Os livros de reprodução fornecem uma tela flexível para análise de dados e a criação de relatórios visuais ricos dentro do portal Azure. Permitem-lhe aceder a várias fontes de dados de todo o Azure e combiná-las em experiências interativas unificadas. 

## <a name="data-sources"></a>Origens de dados

Os livros de trabalho podem consultar dados de várias fontes dentro do Azure. Os autores de livros de trabalho podem transformar estes dados para fornecer informações sobre a disponibilidade, desempenho, utilização e saúde geral dos componentes subjacentes. Por exemplo, analisar registos de desempenho de máquinas virtuais para identificar casos elevados de CPU ou de memória baixa e exibir os resultados como uma grelha num relatório interativo.
  
Mas o verdadeiro poder dos livros de trabalho é a capacidade de combinar dados de fontes díspares num único relatório. Isto permite a criação de pontos de vista de recursos compósitos ou junta-se através de recursos que permitam dados e insights mais ricos que de outra forma seriam impossíveis.

Os livros de trabalho são atualmente compatíveis com as seguintes fontes de dados:

* [Registos](workbooks-data-sources.md#logs)
* [Métricas](workbooks-data-sources.md#metrics)
* [Gráfico de recursos azure](workbooks-data-sources.md#azure-resource-graph)
* [Alertas (Pré-visualização)](workbooks-data-sources.md#alerts-preview)
* [Saúde da carga de trabalho (Pré-visualização)](workbooks-data-sources.md#workload-health-preview)
* [Saúde dos Recursos Azure (Pré-visualização)](workbooks-data-sources.md#azure-resource-health)
* [Explorador de dados azure (pré-visualização)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Visualizações

Os livros de recção fornecem um conjunto rico de capacidades para visualizar os seus dados. Para exemplos detalhados de cada tipo de visualização, pode consultar os links de exemplo abaixo:

* [Text](workbooks-visualizations.md#text) (Texto)
* [Gráficos](workbooks-visualizations.md#charts)
* [Grelhas](workbooks-visualizations.md#grids)
* [Azulejos](workbooks-visualizations.md#tiles)
* [Árvores](workbooks-visualizations.md#trees)
* [Gráficos](workbooks-visualizations.md#graphs)

![Visualizações de livro de exemplo](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Introdução

Para explorar a experiência dos livros de trabalho, navegue primeiro para o serviço Azure Monitor. Isto pode ser feito digitando **monitor** na caixa de pesquisa no portal Azure.

Em seguida, selecione **Livros de Trabalho (pré-visualização)** .

![Screenshot do botão de pré-visualização de livros de trabalho realçado numa caixa vermelha](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galeria

Isto leva-o à galeria dos livros de livros:

![Screenshot da vista da galeria de livros azure Monitor](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Livros versus modelos de livro

Você pode ver um _livro_ em verde e uma série de _modelos_ de livro em roxo. Os modelos servem como relatórios curados que são projetados para uma reutilização flexível por vários utilizadores e equipas. A abertura de um modelo cria um livro transitório povoado com o conteúdo do modelo. 

Pode ajustar os parâmetros do livro baseado no modelo e realizar análises sem medo de quebrar a experiência de reporte futura para os colegas. Se abrir um modelo, faça alguns ajustes e, em seguida, selecione o ícone de salvamento, estará a guardar o modelo como um livro que depois mostraria em verde deixando o modelo original intocado. 

Sob o capot, os modelos também diferem dos livros de trabalho guardados. Salvar um livro cria um recurso associado do Gestor de Recursos Azure, enquanto que o livro transitório criado ao abrir um modelo não tem recursos únicos associados a ele. Para saber mais sobre como o controlo de acesso é gerido nos livros de trabalho consulte o artigo de controlo de acesso dos livros de [livros.](workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Explorando um modelo de livro

Selecione Análise de Falha de **Aplicação** para ver um dos modelos de livro de aplicações padrão.

![Screenshot do modelo de análise de falha de aplicação](./media/workbooks-overview/failure-analysis.png)

Como referido anteriormente, a abertura do modelo cria um livro temporário para que possa interagir. Por padrão, o livro abre no modo de leitura que exibe apenas a informação para a experiência de análise pretendida que foi criada pelo autor do modelo original.

No caso deste livro em particular, a experiência é interativa. Pode ajustar a subscrição, as aplicações direcionadas e o intervalo de tempo dos dados que pretende apresentar. Uma vez feitas essas seleções, a grelha de pedidos http é também interativa, pelo que a seleção de uma linha individual irá alterar os dados que são prestados nas duas tabelas na parte inferior do relatório.

### <a name="editing-mode"></a>Modo de edição

Para entender como este modelo de livro é montado, precisa trocar para o modo de edição selecionando **Editar**. 

![Screenshot do modelo de análise de falha de aplicação](./media/workbooks-overview/edit.png)

Depois de ter mudado para o modo de edição, notará que algumas caixas **de Edição** aparecem corretamente correspondentes a cada aspeto individual do seu livro de trabalho.

![Screenshot do botão Editar](./media/workbooks-overview/edit-mode.png)

Se selecionarmos o botão de edição imediatamente sob a grelha de dados de pedidos, podemos ver que esta parte do nosso livro consiste numa consulta kusto contra dados de um recurso Application Insights.

![Screenshot da consulta subjacente de Kusto](./media/workbooks-overview/kusto.png)

Clicar nos outros botões **Edit** à direita revelará uma série de componentes centrais que compõem livros de livros como caixas de [texto baseadas](workbooks-visualizations.md#text)em marcação, elementos uI de seleção de [parâmetros](workbooks-parameters.md) e outros tipos de [gráfico/visualização](workbooks-visualizations.md). 

Explorar os modelos pré-construídos no modo de edição e, em seguida, modificá-los para se adaptar às suas necessidades e salvar o seu próprio livro personalizado é uma excelente maneira de começar a aprender sobre o que é possível com os livros de trabalho do Azure Monitor.

## <a name="pinning-visualizations"></a>Visualizações de fixação

Os passos de texto, consulta e métricas de um livro podem ser fixados utilizando o botão pin os itens enquanto o livro estiver em modo pin, ou se o autor do livro tiver ativado as definições para que esse elemento torne visível o ícone do pino. 

Para aceder ao modo pin, clique em **Editar** para introduzir o modo de edição e selecione o ícone do pino azul na barra superior. Um ícone de pino individual aparecerá então acima da caixa *de edição* de cada peça de livro correspondente no lado direito do seu ecrã.

![Experiência pin](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> O estado do livro é guardado no momento do pino, e os livros de trabalho fixados num painel de instrumentos não serão atualizados se o livro subjacente for modificado. Para atualizar uma peça de livro fixa, terá de eliminar e voltar a fixar essa peça.

## <a name="dashboard-time-ranges"></a>Intervalos de tempo do painel

As peças de consulta do livro fixas respeitarão o intervalo de tempo do painel de instrumentos se o item fixado estiver configurado para utilizar um parâmetro time *range.* O valor de tempo do painel de instrumentos será usado como valor do parâmetro de intervalo de tempo, e qualquer alteração do intervalo de tempo do painel fará com que o item fixado seja atualizado. Se uma peça fixa estiver a utilizar o intervalo de tempo do painel de instrumentos, verá a legenda da atualização da peça fixa para mostrar o intervalo de tempo do painel de trabalho sempre que o intervalo de tempo se alterar. 

Além disso, as peças de livro fixadas utilizando um parâmetro de intervalo de tempo irão refrescar-se automaticamente a uma taxa determinada pelo intervalo de tempo do painel de instrumentos. A última vez que a consulta correu aparecerá na legenda da parte fixa.

Se um passo fixado tiver um intervalo de tempo explicitamente definido (não utilizar um parâmetro de intervalo de tempo), essa faixa de tempo será sempre utilizada para o painel de instrumentos, independentemente das definições do painel de instrumentos. A legenda da parte fixada não mostrará o intervalo de tempo do painel de instrumentos, e a consulta não atualiza automaticamente no painel de instrumentos. A legenda mostrará a última vez que a consulta foi executada.

> [!NOTE]
> As consultas que utilizam a fonte de dados de *fusão* não são atualmente suportadas quando se fixam nos painéis de instrumentos.

## <a name="sharing-workbook-templates"></a>Compartilhando modelos de livro

Assim que começar a criar os seus próprios modelos de livro, talvez queira partilhá-lo com a comunidade em geral. Para saber mais, e para explorar outros modelos que não fazem parte da vista padrão da galeria Azure Monitor visite o nosso [repositório GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Para consultar os livros existentes, visite a biblioteca do [Livro](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) no GitHub.

## <a name="next-step"></a>Passo seguinte

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
