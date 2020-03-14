---
title: Fluxos de dados de mapeamento
description: Uma visão geral dos fluxos de dados de mapeamento na Fábrica de Dados Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 210c1814325e689dd70af9caa7fad08deed933e1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243799"
---
# <a name="what-are-mapping-data-flows"></a>O que são fluxos de dados de mapeamento?

Os fluxos de dados de mapeamento são transformações de dados projetadas visualmente na Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam lógica de transformação gráfica de dados sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro dos oleodutos da Azure Data Factory que utilizam clusters de faíscas escaldados. As atividades de fluxo de dados podem ser operacionalizadas através das capacidades de agendamento, controlo, fluxo e monitorização existentes da Data Factory.

Os fluxos de dados de mapeamento proporcionam uma experiência totalmente visual, sem necessidade de codificação. Os seus fluxos de dados serão executados no seu próprio cluster de execução para processamento de dados em escala. A Azure Data Factory trata de toda a tradução de código, otimização de caminhos e execução dos seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Para criar um fluxo de dados, selecione o sinal de mais em recursos **de fábrica**e, em seguida, selecione **Data Flow**. 

![Novo fluxo de dados](media/data-flow/newdataflow2.png "novo fluxo de dados")

Isto leva-o à tela de fluxo de dados onde pode criar a sua lógica de transformação. Selecione **Adicionar fonte** para começar a configurar a sua transformação de origem. Para mais informações, consulte a [transformação de Origem.](data-flow-source.md)

## <a name="data-flow-canvas"></a>Tela de fluxo de dados

A tela de fluxo de dados é separada em três partes: a barra superior, o gráfico e o painel de configuração. 

![Tela](media/data-flow/canvas1.png "Tela")

### <a name="graph"></a>Graph

O gráfico exibe o fluxo de transformação. Mostra a linhagem dos dados de origem à medida que flui para um ou mais lavatórios. Para adicionar uma nova fonte, **selecione Adicionar fonte**. Para adicionar uma nova transformação, selecione o sinal de mais no direito inferior de uma transformação existente.

![Tela](media/data-flow/canvas2.png "Tela")

### <a name="azure-integration-runtime-data-flow-properties"></a>Propriedades de fluxo de dados de tempo de execução de integração Azure

![Botão de depuração](media/data-flow/debugbutton.png "Botão de depuração")

Quando começar a trabalhar com fluxos de dados em ADF, vai querer ligar o interruptor "Debug" para fluxos de dados no topo da UI do navegador. Isto irá girar um cluster de Databricks Azure para usar para depuração interativa, pré-visualizações de dados e execuções de depuração de gasodutos. Pode definir o tamanho do cluster que está a ser utilizado escolhendo um Tempo de Execução de [Integração Azure](concepts-integration-runtime.md)personalizado. A sessão de depuração permanecerá viva até 60 minutos após a sua última pré-visualização de dados ou a última execução do gasoduto de depuração.

Quando operacionalizar os seus oleodutos com atividades de fluxo de dados, a ADF utilizará o Tempo de Funcionamento de Integração Azure associado à [atividade](control-flow-execute-data-flow-activity.md) na propriedade "Run On".

O tempo de execução de integração azure padrão é um pequeno cluster de nó de 4 núcleos single worker destinado a permitir-lhe visualizar dados e executar rapidamente gasodutos de depuração a custos mínimos. Detete uma configuração de IR Azure maior se estiver a realizar operações contra grandes conjuntos de dados.

Pode instruir a ADF a manter um conjunto de recursos de cluster (VMs) através da definição de um TTL nas propriedades de fluxo de dados do Azure IR. Isto resultará numa execução mais rápida do emprego em atividades subsequentes.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Estratégias de tempo de integração azure e de fluxo de dados

##### <a name="execute-data-flows-in-parallel"></a>Executar fluxos de dados em paralelo

Se executar fluxos de dados num pipeline em paralelo, a ADF irá girar os clusters separados de Bricks Azure para cada execução de atividade com base nas definições do seu Tempo de Execução de Integração Azure anexado a cada atividade. Para conceber execuções paralelas em pipelines ADF, adicione as suas atividades de fluxo de dados sem restrições de precedência na UI.

Destas três opções, esta opção provavelmente executará no menor espaço de tempo. No entanto, cada fluxo de dados paralelos será executado ao mesmo tempo em clusters separados, pelo que a ordenação de eventos não é determinista.

Se estiver a executar as suas atividades de fluxo de dados paralelamente dentro dos seus oleodutos, recomenda-se que não utilize tTL. Isto porque as execuções paralelas de fluxos de dados simultaneamente utilizando o mesmo Tempo de Execução de Integração Azure resultarão em múltiplas instâncias quentes de piscina para a sua fábrica de dados.

##### <a name="overload-single-data-flow"></a>Sobrecarga fluxo de dados únicos

Se colocar toda a sua lógica dentro de um único fluxo de dados, a ADF executará todos no mesmo contexto de execução de emprego numa única instância de cluster Spark.

Esta opção pode ser mais difícil de seguir e resolver problemas porque as suas regras de negócio e lógica de negócio saem juntas. Esta opção também não proporciona muita reutilização.

##### <a name="execute-data-flows-serially"></a>Executar fluxos de dados em série

Se executar as suas atividades de fluxo de dados em série no pipeline e tiver definido um TTL na configuração Do IR Do Azure, então a ADF reutilizará os recursos de computação (VMs) resultando em tempos de execução posteriores mais rápidos. Ainda receberá um novo contexto spark para cada execução.

Destas três opções, esta provavelmente levará mais tempo para executar de ponta a ponta. Mas proporciona uma separação limpa das operações lógicas em cada passo de fluxo de dados.

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as definições específicas da transformação atualmente selecionada. Se não for selecionada nenhuma transformação, mostra o fluxo de dados. Na configuração geral do fluxo de dados, pode editar o nome e a descrição sob o separador **Geral** ou adicionar parâmetros através do separador **Parâmetros.** Para obter mais informações, consulte os parâmetros de fluxo de [dados de mapeamento](parameters-data-flow.md).

Cada transformação tem pelo menos quatro separadores de configuração.

#### <a name="transformation-settings"></a>Definições de transformação

O primeiro separador no painel de configuração de cada transformação contém as definições específicas dessa transformação. Para mais informações, consulte a página de documentação da transformação.

![Separador de definições de origem](media/data-flow/source1.png "Separador de definições de origem")

#### <a name="optimize"></a>Otimizar

O separador **Otimize** contém definições para configurar esquemas de partição.

![Optimize](media/data-flow/optimize1.png "Otimizar")

A definição predefinida é **utilizar a partição atual,** que instrui a Azure Data Factory a utilizar o esquema de partição nativo dos fluxos de dados em funcionamento na Spark. Na maioria dos cenários, recomendamos esta configuração.

Há casos em que talvez queira ajustar a divisão. Por exemplo, se quiser fazer as suas transformações para um único ficheiro no lago, selecione **uma única divisória** numa transformação de pia.

Outro caso em que poderá querer controlar os esquemas de partição é otimizar o desempenho. O ajuste da divisória fornece controlo sobre a distribuição dos seus dados através de nós de cálculo e otimizações de localização de dados que podem ter efeitos positivos e negativos no desempenho global do fluxo de dados. Para mais informações, consulte o guia de desempenho do fluxo de [dados](concepts-data-flow-performance.md).

Para alterar a partilha em qualquer transformação, selecione o separador **Otimize** e selecione o botão de rádio **DePartição Definido.** Em seguida, ser-lhe-á apresentada uma série de opções para a partilha. O melhor método de partilha diferirá com base nos volumes de dados, chaves dos candidatos, valores nulos e cardinalidade. 

Uma boa prática é começar com a partilha padrão e, em seguida, tentar diferentes opções de partição. Pode testar utilizando execuções de depuração de gasodutos e visualizar o tempo de execução e o uso da partilha em cada agrupamento de transformação a partir da vista de monitorização. Para obter mais informações, consulte [monitorização dos fluxos](concepts-data-flow-monitoring.md)de dados .

Estão disponíveis as seguintes opções de partição.

##### <a name="round-robin"></a>Robin redondo 

Robin redondo é uma simples divisória que distribui automaticamente dados igualmente através de divisórias. Use o robin redondo quando não tiver bons candidatos-chave para implementar uma estratégia sólida e inteligente de parto. Pode definir o número de divisórias físicas.

##### <a name="hash"></a>Hash

A Azure Data Factory produzirá um hash de colunas para produzir divisórias uniformes de modo a que as linhas com valores semelhantes caiam na mesma divisória. Quando utilizar a opção Hash, teste para uma possível distorção da divisória. Pode definir o número de divisórias físicas.

##### <a name="dynamic-range"></a>Gama dinâmica

A gama dinâmica utilizará gamas dinâmicas spark com base nas colunas ou expressões que fornece. Pode definir o número de divisórias físicas. 

##### <a name="fixed-range"></a>Gama fixa

Construa uma expressão que forneça uma gama fixa de valores dentro das suas colunas de dados divididas. Para evitar distorções de divisórias, deve ter uma boa compreensão dos seus dados antes de utilizar esta opção. Os valores que introduzir para a expressão serão usados como parte de uma função de partição. Pode definir o número de divisórias físicas.

##### <a name="key"></a>Chave

Se tiver uma boa compreensão da cardinalidade dos seus dados, a divisão chave pode ser uma boa estratégia. A partição chave criará divisórias para cada valor único na sua coluna. Não é possível definir o número de divisórias porque o número será baseado em valores únicos nos dados.

#### <a name="inspect"></a>Inspecionar

O separador **Inspecionar** fornece uma visão dos metadados do fluxo de dados que está a transformar. Pode ver as contagens das colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordem da coluna e as referências da coluna. **Inspecionar** é uma visão apenas para leitura dos seus metadados. Não é necessário ter o modo de depuração ativado para ver metadados no painel **'Inspeccionar'.**

![Inspecionar](media/data-flow/inspect1.png "Inspecionar")

À medida que muda a forma dos seus dados através de transformações, verá as alterações dos metadados fluírem no painel **De si.** Se não houver um esquema definido na sua transformação de origem, então os metadados não serão visíveis no painel **de inspeção.** A falta de metadados é comum em cenários de deriva de esquemas.

#### <a name="data-preview"></a>Pré-visualização de dados

Se o modo dedepurar estiver ligado, o separador **'Preview'** de dados dá-lhe uma imagem interativa dos dados em cada transformação. Para mais informações, consulte a [pré-visualização de Dados no modo de depuração](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como a poupança e validação. Também pode alternar entre os modos de gráfico e configuração utilizando os botões **Do Gráfico** de Série e Do Gráfico **de Ocultar.**

![Ocultar gráfico](media/data-flow/hideg.png "Ocultar gráfico")

Se esconder o seu gráfico, pode navegar pelos seus nós de transformação lateralmente através dos botões **Anterior** e **Seguinte.**

![Botões anteriores e próximos](media/data-flow/showhide.png "botões anteriores e próximos")

## <a name="next-steps"></a>Passos seguintes

* Aprenda a criar uma [transformação de origem.](data-flow-source.md)
* Aprenda a construir os seus fluxos de dados no [modo de depuração](concepts-data-flow-debug-mode.md).
