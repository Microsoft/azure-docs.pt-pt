---
title: Fluxos de dados
description: Uma visão geral dos fluxos de dados no Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592668"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Fluxos de dados em Azure Synapse Analytics

## <a name="what-are-data-flows"></a>O que são fluxos de dados?

Os fluxos de dados são transformações de dados visualmente projetadas no Azure Synapse Analytics. Os fluxos de dados permitem que os engenheiros de dados desenvolvam lógicas de transformação de dados sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro dos oleodutos Azure Synapse Analytics que utilizam clusters Apache Spark escalonados. As atividades de fluxo de dados podem ser operacionalizadas utilizando as capacidades de agendamento, controlo, fluxo e monitorização existentes do Azure Synapse Analytics.

Os fluxos de dados proporcionam uma experiência inteiramente visual sem necessidade de codificação. Os fluxos de dados são executados em clusters de execução geridos pela Synapse para processamento de dados em escala. O Azure Synapse Analytics trata de toda a tradução de código, otimização de caminhos e execução dos seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Os fluxos de dados são criados a partir do painel Develop in Synapse studio. Para criar um fluxo de dados, selecione o sinal de mais ao lado **do Develop** e, em seguida, selecione **Data Flow**. 

![Novo fluxo de dados](media/data-flow/new-data-flow.png)

Esta ação leva-o à tela do fluxo de dados, onde pode criar a sua lógica de transformação. **Selecione Adicionar fonte** para começar a configurar a sua transformação de origem. Para obter mais informações, consulte [a transformação de Fonte.](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="authoring-data-flows"></a>Fluxos de dados de autoria

O fluxo de dados tem uma tela de autoria única projetada para facilitar a lógica de transformação do edifício. A tela de fluxo de dados é separada em três partes: a barra superior, o gráfico e o painel de configuração. 

![A screenshot mostra a tela de fluxo de dados com barra superior, gráfico e painel de configuração rotulado.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

O gráfico mostra o fluxo de transformação. Mostra a linhagem de dados de origem à medida que flui para um ou mais sumidouros. Para adicionar uma nova fonte, **selecione Adicionar fonte**. Para adicionar uma nova transformação, selecione o sinal de mais no canto inferior direito de uma transformação existente. Saiba mais sobre como [gerir o gráfico de fluxo de dados.](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![A screenshot mostra a parte do gráfico da tela com uma caixa de texto de pesquisa.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as definições específicas da transformação atualmente selecionada. Se nenhuma transformação for selecionada, mostra o fluxo de dados. Na configuração geral do fluxo de dados, pode adicionar parâmetros através do **separador Parâmetros.** Para obter mais informações, consulte [os parâmetros de fluxo de dados.](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

Cada transformação contém pelo menos quatro separadores de configuração.

#### <a name="transformation-settings"></a>Definições de transformação

O primeiro separador no painel de configuração de cada transformação contém as definições específicas dessa transformação. Para mais informações, consulte a página de documentação da transformação.

![Separador de definições de origem](media/data-flow/source-1.png)

#### <a name="optimize"></a>Otimização

O **separador Otimize** contém configurações para configurar esquemas de partição. Para saber mais sobre como otimizar os fluxos de dados, consulte o guia de desempenho do [fluxo de dados de mapeamento.](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

![Screenshot mostra o separador Otimize](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecione

O **separador Inspect** fornece uma visão dos metadados do fluxo de dados que está a transformar. Pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem da coluna e as referências de colunas. **Inspecione-se** uma visão apenas de leitura dos seus metadados. Não é necessário ter o modo de depuragem habilitado para ver metadados no painel **de inspeção.**

![Guia de inspeção](media/data-flow/inspect.png)

À medida que altera a forma dos seus dados através de transformações, verá as alterações dos metadados fluem no painel **de inspeção.** Se não houver um esquema definido na sua transformação de origem, então os metadados não serão visíveis no painel **de inspeção.** A falta de metadados é comum em cenários de deriva de esquemas.

#### <a name="data-preview"></a>Pré-visualização dos dados

Se o modo de depuração estiver ligado, o **separador Data Preview** dá-lhe uma imagem interativa dos dados em cada transformação. Para obter mais informações, consulte [a pré-visualização de dados no modo depuragem](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como validação e definições de depuração. Também pode ver o código JSON subjacente e o roteiro do fluxo de dados da sua lógica de transformação.

## <a name="available-transformations"></a>Transformações disponíveis

Veja a visão geral da transformação do [fluxo de dados de mapeamento](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter uma lista de transformações disponíveis.

## <a name="data-flow-activity"></a>Atividade de fluxo de dados

Os fluxos de dados são operacionalizados nos oleodutos Azure Synapse Analytics utilizando a atividade de [fluxo de dados](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Tudo o que um utilizador tem de fazer é especificar qual o tempo de execução de integração a utilizar e passar em valores de parâmetros. Para mais informações, saiba mais sobre o tempo de integração do [Azure.](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime)

## <a name="debug-mode"></a>Modo de depuração

O modo Debug permite-lhe ver interativamente os resultados de cada passo de transformação enquanto constrói e depura os seus fluxos de dados. A sessão de depurar pode ser usada tanto na construção da sua lógica de fluxo de dados como na depuragem do pipeline de execução com atividades de fluxo de dados. Para saber mais, consulte a documentação do [modo de depuração](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Monitorização dos fluxos de dados

O fluxo de dados integra-se com as capacidades de monitorização Azure Synapse Analytics existentes. Para aprender a compreender a produção de monitorização do fluxo de dados, consulte [a monitorização dos fluxos de dados de mapeamento](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

A equipa Azure Synapse Analytics criou um [guia de afinação de desempenho](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para ajudá-lo a otimizar o tempo de execução dos seus fluxos de dados após a construção da sua lógica de negócio.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a criar uma [transformação de origem.](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* Saiba como construir os fluxos de dados no [modo de depuragem](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
