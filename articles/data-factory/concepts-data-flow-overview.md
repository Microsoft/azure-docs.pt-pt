---
title: Fluxos de dados de mapeamento
description: Uma visão geral dos fluxos de dados de mapeamento na Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: 850879675d4554329f24c86f2ac28660b303084c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475571"
---
# <a name="what-are-mapping-data-flows"></a>O que são fluxos de dados de mapeamento?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Os fluxos de dados de mapeamento são transformações de dados visualmente projetadas na Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam lógicas de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro dos oleodutos da Azure Data Factory que utilizam clusters Apache Spark escalonados. As atividades de fluxo de dados podem ser contratadas através da agendamento, controlo, fluxo e capacidades de monitorização existentes da Data Factory.

Os fluxos de dados de mapeamento proporcionam uma experiência inteiramente visual sem necessidade de codificação. Os fluxos de dados são executados no seu cluster de execução para processamento de dados em escala. A Azure Data Factory trata de toda a tradução de código, otimização de caminhos e execução dos seus trabalhos de fluxo de dados.

![Arquitetura](media/data-flow/adf-data-flows.png "Arquitetura")

## <a name="getting-started"></a>Introdução

Para criar um fluxo de dados, selecione o sinal de mais em **Recursos de Fábrica**e, em seguida, selecione Data **Flow**. 

![Novo fluxo de dados](media/data-flow/newdataflow2.png "novo fluxo de dados")

Esta ação leva-o à tela do fluxo de dados, onde pode criar a sua lógica de transformação. **Selecione Adicionar fonte** para começar a configurar a sua transformação de origem. Para obter mais informações, consulte [a transformação de Fonte.](data-flow-source.md)

## <a name="data-flow-canvas"></a>Tela de fluxo de dados

A tela de fluxo de dados é separada em três partes: a barra superior, o gráfico e o painel de configuração. 

![Telas](media/data-flow/canvas1.png "Telas")

### <a name="graph"></a>Graph

O gráfico mostra o fluxo de transformação. Mostra a linhagem de dados de origem à medida que flui para um ou mais sumidouros. Para adicionar uma nova fonte, **selecione Adicionar fonte**. Para adicionar uma nova transformação, selecione o sinal de mais no canto inferior direito de uma transformação existente.

![Telas](media/data-flow/canvas2.png "Telas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Propriedades de fluxo de dados de fluxo de tempo de integração Azure

![Botão Depurg](media/data-flow/debugbutton.png "Botão Depurg")

Quando começar a trabalhar com fluxos de dados em ADF, pretende ligar o interruptor "Debug" para fluxos de dados no topo da UI do navegador. Este spins-up um cluster Spark para usar para depuração interativa, pré-visualizações de dados e execuções de depuração de gasodutos. Pode definir o tamanho do cluster que está a ser utilizado escolhendo um tempo de execução de [integração Azure](concepts-integration-runtime.md)personalizado. A sessão de depurar permanece viva até 60 minutos após a pré-visualização dos seus últimos dados ou a última execução do gasoduto de depuragem.

Ao operacionalizar os seus oleodutos com atividades de fluxo de dados, a ADF utiliza o Tempo de Execução de Integração Azure associado à [atividade](control-flow-execute-data-flow-activity.md) na propriedade "Run On".

O padrão Azure Integration Runtime é um pequeno cluster de nó de trabalhador único de 4 núcleos que permite visualizar dados e executar rapidamente os gasodutos de depuração a custos mínimos. Defina uma configuração de IR Azure maior se estiver a realizar operações contra grandes conjuntos de dados.

Pode instruir a ADF a manter um conjunto de recursos de cluster (VMs) definindo um TTL nas propriedades de fluxo de dados Azure IR. Esta ação resulta numa execução mais rápida do emprego nas atividades subsequentes.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Estratégias de execução e fluxo de dados da Azure

##### <a name="execute-data-flows-in-parallel"></a>Executar fluxos de dados em paralelo

Se executar fluxos de dados num oleoduto em paralelo, a ADF gira os agrupamentos de faíscas separados para cada execução de atividade com base nas definições do seu Tempo de Execução de Integração Azure ligado a cada atividade. Para conceber execuções paralelas em oleodutos ADF, adicione as suas atividades de fluxo de dados sem restrições de precedência na UI.

Destas três opções, esta opção provavelmente executa no menor período de tempo. No entanto, cada fluxo de dados paralelo executa ao mesmo tempo em clusters separados, pelo que a ordem dos eventos não é determinística.

Se estiver a executar as suas atividades de fluxo de dados em paralelo dentro dos seus oleodutos, recomenda-se que não utilize TTL. Esta ação deve-se ao facto de as execuções paralelas do seu fluxo de dados utilizarem simultaneamente o mesmo Tempo de Execução de Integração Azure resulta em múltiplas instâncias de piscina quente para a sua fábrica de dados.

##### <a name="overload-single-data-flow"></a>Sobrecarga fluxo de dados únicos

Se colocar toda a sua lógica dentro de um único fluxo de dados, a ADF executa o mesmo contexto de execução de emprego numa única instância de cluster Spark.

Esta opção pode ser mais desafiante para seguir e resolver problemas porque as suas regras de negócio e lógica de negócio podem ser misturadas. Esta opção também não proporciona muita reutilização.

##### <a name="execute-data-flows-sequentially"></a>Executar fluxos de dados sequencialmente

Se executar as suas atividades de fluxo de dados em sequência no oleoduto e tiver definido um TTL na configuração Azure IR, então a ADF reutilizará os recursos de cálculo (VMs) resultando em tempos de execução posteriores mais rápidos. Ainda receberá um novo contexto spark para cada execução.

Destas três opções, esta ação provavelmente demora mais tempo a executar de ponta a ponta. Mas fornece uma separação limpa das operações lógicas em cada passo de fluxo de dados.

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as definições específicas da transformação atualmente selecionada. Se nenhuma transformação for selecionada, mostra o fluxo de dados. Na configuração geral do fluxo de dados, pode editar o nome e a descrição no separador **Geral** ou adicionar parâmetros através do **separador Parâmetros.** Para obter mais informações, consulte [os parâmetros de fluxo de dados de mapeamento.](parameters-data-flow.md)

Cada transformação contém pelo menos quatro separadores de configuração.

#### <a name="transformation-settings"></a>Definições de transformação

O primeiro separador no painel de configuração de cada transformação contém as definições específicas dessa transformação. Para mais informações, consulte a página de documentação da transformação.

![Separador de definições de origem](media/data-flow/source1.png "Separador de definições de origem")

#### <a name="optimize"></a>Otimizar

O **separador Otimize** contém configurações para configurar esquemas de partição. Para saber mais sobre como otimizar os fluxos de dados, consulte o guia de desempenho do [fluxo de dados de mapeamento.](concepts-data-flow-performance.md)

![Otimizar](media/data-flow/optimize.png "Otimizar")

#### <a name="inspect"></a>Inspecione

O **separador Inspect** fornece uma visão dos metadados do fluxo de dados que está a transformar. Pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem da coluna e as referências de colunas. **Inspecione-se** uma visão apenas de leitura dos seus metadados. Não é necessário ter o modo de depuragem habilitado para ver metadados no painel **de inspeção.**

![Inspecione](media/data-flow/inspect1.png "Inspecione")

À medida que altera a forma dos seus dados através de transformações, verá as alterações dos metadados fluem no painel **de inspeção.** Se não houver um esquema definido na sua transformação de origem, então os metadados não serão visíveis no painel **de inspeção.** A falta de metadados é comum em cenários de deriva de esquemas.

#### <a name="data-preview"></a>Pré-visualização dos dados

Se o modo de depuração estiver ligado, o **separador Data Preview** dá-lhe uma imagem interativa dos dados em cada transformação. Para obter mais informações, consulte [a pré-visualização de dados no modo depuragem](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como a poupança e validação. Também pode alternar entre os modos de gráfico e configuração utilizando os botões **'Gráfico de Exibição'** e **Ocultar** gráficos.

![Ocultar gráfico](media/data-flow/hideg.png "Ocultar gráfico")

Se ocultar o seu gráfico, pode navegar pelos seus nós de transformação lateralmente através dos botões **Anterior** e **Seguinte.**

![Botões anteriores e seguintes](media/data-flow/showhide.png "botões anteriores e próximos")

## <a name="next-steps"></a>Passos seguintes

* Aprenda a criar uma [transformação de origem.](data-flow-source.md)
* Saiba como construir os fluxos de dados no [modo de depuragem](concepts-data-flow-debug-mode.md).
