---
title: Fluxos de dados de mapeamento
description: Uma visão geral dos fluxos de dados de mapeamento na Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/10/2020
ms.openlocfilehash: f78cbaedea90a241efddd0eff9cb7503201528c5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590411"
---
# <a name="mapping-data-flows-in-azure-data-factory"></a>Mapeamento de fluxos de dados na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="what-are-mapping-data-flows"></a>O que são fluxos de dados de mapeamento?

Os fluxos de dados de mapeamento são transformações de dados visualmente projetadas na Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam lógicas de transformação de dados sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro dos oleodutos da Azure Data Factory que utilizam clusters Apache Spark escalonados. As atividades de fluxo de dados podem ser operacionalizadas utilizando as capacidades de agendamento, controlo, fluxo e monitorização existentes da Azure Data Factory.

Os fluxos de dados de mapeamento proporcionam uma experiência inteiramente visual sem necessidade de codificação. Os fluxos de dados são executados em clusters de execução geridos pela ADF para processamento de dados em escala. A Azure Data Factory trata de toda a tradução de código, otimização de caminhos e execução dos seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Os fluxos de dados são criados a partir do painel de recursos de fábrica, como oleodutos e conjuntos de dados. Para criar um fluxo de dados, selecione o sinal de mais ao lado **dos Recursos de Fábrica** e, em seguida, selecione Data **Flow**. 

![Novo fluxo de dados](media/data-flow/new-data-flow.png)

Esta ação leva-o à tela do fluxo de dados, onde pode criar a sua lógica de transformação. **Selecione Adicionar fonte** para começar a configurar a sua transformação de origem. Para obter mais informações, consulte [a transformação de Fonte.](data-flow-source.md)

## <a name="authoring-data-flows"></a>Fluxos de dados de autoria

O fluxo de dados de mapeamento tem uma tela de autoria única projetada para tornar a lógica de transformação de construção fácil. A tela de fluxo de dados é separada em três partes: a barra superior, o gráfico e o painel de configuração. 

![A screenshot mostra a tela de fluxo de dados com barra superior, gráfico e painel de configuração rotulado.](media/data-flow/canvas-1.png "Telas")

### <a name="graph"></a>Graph

O gráfico mostra o fluxo de transformação. Mostra a linhagem de dados de origem à medida que flui para um ou mais sumidouros. Para adicionar uma nova fonte, **selecione Adicionar fonte**. Para adicionar uma nova transformação, selecione o sinal de mais no canto inferior direito de uma transformação existente. Saiba mais sobre como [gerir o gráfico de fluxo de dados.](concepts-data-flow-manage-graph.md)

![A screenshot mostra a parte do gráfico da tela com uma caixa de texto de pesquisa.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as definições específicas da transformação atualmente selecionada. Se nenhuma transformação for selecionada, mostra o fluxo de dados. Na configuração geral do fluxo de dados, pode adicionar parâmetros através do **separador Parâmetros.** Para obter mais informações, consulte [os parâmetros de fluxo de dados de mapeamento.](parameters-data-flow.md)

Cada transformação contém pelo menos quatro separadores de configuração.

#### <a name="transformation-settings"></a>Definições de transformação

O primeiro separador no painel de configuração de cada transformação contém as definições específicas dessa transformação. Para mais informações, consulte a página de documentação da transformação.

![Separador de definições de origem](media/data-flow/source1.png "Separador de definições de origem")

#### <a name="optimize"></a>Otimizar

O **separador Otimize** contém configurações para configurar esquemas de partição. Para saber mais sobre como otimizar os fluxos de dados, consulte o guia de desempenho do [fluxo de dados de mapeamento.](concepts-data-flow-performance.md)

![O screenshot mostra o separador Otimize, que inclui a opção de partição, o tipo de partição e o número de divisórias.](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecione

O **separador Inspect** fornece uma visão dos metadados do fluxo de dados que está a transformar. Pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem da coluna e as referências de colunas. **Inspecione-se** uma visão apenas de leitura dos seus metadados. Não é necessário ter o modo de depuragem habilitado para ver metadados no painel **de inspeção.**

![Inspecione](media/data-flow/inspect1.png "Inspecione")

À medida que altera a forma dos seus dados através de transformações, verá as alterações dos metadados fluem no painel **de inspeção.** Se não houver um esquema definido na sua transformação de origem, então os metadados não serão visíveis no painel **de inspeção.** A falta de metadados é comum em cenários de deriva de esquemas.

#### <a name="data-preview"></a>Pré-visualização dos dados

Se o modo de depuração estiver ligado, o **separador Data Preview** dá-lhe uma imagem interativa dos dados em cada transformação. Para obter mais informações, consulte [a pré-visualização de dados no modo depuragem](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como a poupança e validação. Também pode ver o código JSON subjacente e o roteiro do fluxo de dados da sua lógica de transformação. Para mais informações, saiba mais sobre o script do [fluxo de dados.](data-flow-script.md)

## <a name="available-transformations"></a>Transformações disponíveis

Veja a visão geral da transformação do [fluxo de dados de mapeamento](data-flow-transformation-overview.md) para obter uma lista de transformações disponíveis.

## <a name="data-flow-activity"></a>Atividade de fluxo de dados

Os fluxos de dados de mapeamento são operacionalizados dentro dos oleodutos ADF utilizando a [atividade de fluxo de dados](control-flow-execute-data-flow-activity.md). Tudo o que um utilizador tem de fazer é especificar qual o tempo de execução de integração a utilizar e passar em valores de parâmetros. Para mais informações, saiba mais sobre o tempo de integração do [Azure.](concepts-integration-runtime.md#azure-integration-runtime)

## <a name="debug-mode"></a>Modo de depuração

O modo Debug permite-lhe ver interativamente os resultados de cada passo de transformação enquanto constrói e depura os seus fluxos de dados. A sessão de depurar pode ser usada tanto na construção da sua lógica de fluxo de dados como na depuragem do pipeline de execução com atividades de fluxo de dados. Para saber mais, consulte a documentação do [modo de depuração](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flows"></a>Monitorização dos fluxos de dados

O fluxo de dados de mapeamento integra-se com as capacidades de monitorização da Azure Data Factory existentes. Para aprender a compreender a produção de monitorização do fluxo de dados, consulte [a monitorização dos fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

A equipa da Azure Data Factory criou um [guia de afinação de desempenho](concepts-data-flow-performance.md) para o ajudar a otimizar o tempo de execução dos seus fluxos de dados após a construção da sua lógica de negócio.

## <a name="available-regions"></a>Regiões disponíveis

Os fluxos de dados de mapeamento estão disponíveis nas seguintes regiões em ADF:

| Região do Azure | Fluxos de dados em ADF |
| ------------ | ----------------- |
|  Austrália Central | |
| Austrália Central 2 | |
| Leste da Austrália | ✓ |
| Sudeste da Austrália   | ✓ |
| Sul do Brasil  | ✓ |
| Canadá Central | ✓ |
| Índia Central | ✓ |
| E.U.A. Central    | ✓ |
| Leste da China |      |
| China Leste 2  |   |
| China Não-Regional | |
| Norte da China |     |
| China Norte 2 | |
| Ásia Leste | ✓ |
| E.U.A. Leste   | ✓ |
| E.U.A. Leste 2 | ✓ |
| França Central | ✓ |
| Sul de França  | |
| Alemanha Central (Soberano) | |
| Alemanha Não Regional (Soberano) | |
| Alemanha Norte (Público) | |
| Nordeste da Alemanha (Soberano) | |
| Alemanha Centro Ocidental (Público) |  |
| Leste do Japão | ✓ |
| Oeste do Japão |  |
| Coreia do Sul Central | ✓ |
| Sul da Coreia do Sul | |
| E.U.A. Centro-Norte  | ✓ |
| Europa do Norte  | ✓ |
| Leste da Noruega | |
| Oeste da Noruega | |
| Norte da África do Sul    | ✓ |
| Oeste da África do Sul |  |
| E.U.A. Centro-Sul  | |
| Sul da Índia | |
| Sudeste Asiático    | ✓ |
| Suíça Norte |   |
| Suíça Oeste | |
| Centro dos Emirados Árabes Unidos | |
| Uae Norte |  |
| Sul do Reino Unido  | ✓ |
| Oeste do Reino Unido |     |
| US DoD Centro | |
| US DoD - Leste | |
| US Gov - Arizona |      |
| Gov dos EUA não-regional | |
| US Gov - Texas | |
| US Gov - Virginia |     |
| E.U.A. Centro-Oeste |     |
| Europa Ocidental   | ✓ |
| Oeste da Índia | |
| E.U.A. Oeste   | ✓ |
| E.U.A. Oeste 2 | ✓ |

## <a name="next-steps"></a>Passos seguintes

* Aprenda a criar uma [transformação de origem.](data-flow-source.md)
* Saiba como construir os fluxos de dados no [modo de depuragem](concepts-data-flow-debug-mode.md).
