---
title: Fluxos de dados de mapeamento
description: Uma visão geral do mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/7/2019
ms.openlocfilehash: 397ecdb805f0be9f374c53ae7128f806bfb789d3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928295"
---
# <a name="what-are-mapping-data-flows"></a>O que são fluxos de dados de mapeamento?

O mapeamento de fluxos de dados são transformações de dados visualmente projetadas em Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro de Azure Data Factory pipelines que usam clusters Spark expandidos. As atividades de fluxo de dados podem ser operadas por meio de recursos de agendamento, controle, fluxo e monitoramento existentes de Data Factory.

O mapeamento de fluxos de dados fornece uma experiência totalmente visual sem a necessidade de codificação. Seus fluxos de dados serão executados em seu próprio cluster de execução para processamento de dados expandido. Azure Data Factory manipula toda a conversão de código, a otimização de caminho e a execução de seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Para criar um fluxo de dados, selecione o sinal de adição em **recursos de fábrica**e, em seguida, selecione **fluxo de dados**. 

![Novo fluxo de dados](media/data-flow/newdataflow2.png "novo fluxo de dados")

Isso levará você para a tela de fluxo de dados, na qual você pode criar a lógica de transformação. Selecione **Adicionar origem** para começar a configurar sua transformação de origem. Para obter mais informações, consulte [Source Transformation](data-flow-source.md).

## <a name="data-flow-canvas"></a>Tela de fluxo de dados

A tela de fluxo de dados é separada em três partes: a barra superior, o grafo e o painel de configuração. 

![Tela](media/data-flow/canvas1.png "Tela")

### <a name="graph"></a>Graph

O grafo exibe o fluxo de transformação. Ele mostra a linhagem dos dados de origem conforme eles fluem em um ou mais coletores. Para adicionar uma nova origem, selecione **Adicionar origem**. Para adicionar uma nova transformação, selecione o sinal de adição no canto inferior direito de uma transformação existente.

![Tela](media/data-flow/canvas2.png "Tela")

### <a name="azure-integration-runtime-data-flow-properties"></a>Propriedades de fluxo de dados do Azure Integration Runtime

![Botão de depuração](media/data-flow/debugbutton.png "Botão de depuração")

Ao começar a trabalhar com fluxos de dados no ADF, você desejará ativar a opção de "depuração" para fluxos de dados na parte superior da interface do usuário do navegador. Isso fará com que um cluster de Azure Databricks seja girado para uso para depuração interativa, visualizações de dados e execuções de depuração de pipeline. Você pode definir o tamanho do cluster que está sendo utilizado escolhendo um [Azure Integration Runtime](concepts-integration-runtime.md)personalizado. A sessão de depuração permanecerá ativa por até 60 minutos após a última visualização de dados ou a última execução do pipeline de depuração.

Quando você colocar seus pipelines em operação com atividades de fluxo de dados, o ADF usará o Azure Integration Runtime associado à [atividade](control-flow-execute-data-flow-activity.md) na propriedade "executar em".

O Azure Integration Runtime padrão é um cluster de nó de trabalho único de 4 núcleos, destinado a permitir a visualização de dados e a execução rápida de pipelines de depuração com custos mínimos. Defina uma configuração de Azure IR maior se você estiver executando operações em grandes conjuntos de altos.

Você pode instruir o ADF a manter um pool de recursos de cluster (VMs) definindo um TTL na Azure IR Propriedades de fluxo de dados. Isso resultará em uma execução de trabalho mais rápida nas atividades subsequentes.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Tempo de execução de integração do Azure e estratégias de fluxo de dados

##### <a name="execute-data-flows-in-parallel"></a>Executar fluxos de dados em paralelo

Se você executar fluxos de dados em um pipeline em paralelo, o ADF girará Azure Databricks clusters separados para cada execução de atividade com base nas configurações de sua Azure Integration Runtime anexadas a cada atividade. Para criar execuções paralelas em pipelines do ADF, adicione suas atividades de fluxo de dados sem restrições de precedência na interface do usuário.

Dessas três opções, essa opção provavelmente será executada no menor período de tempo. No entanto, cada fluxo de dados paralelo será executado ao mesmo tempo em clusters separados, de modo que a ordem dos eventos é não determinística.

##### <a name="overload-single-data-flow"></a>Sobrecarga de fluxo de dados único

Se você colocar toda a lógica dentro de um único fluxo de dados, o ADF será executado no mesmo contexto de execução de trabalho em uma única instância de cluster do Spark.

Essa opção pode ser mais difícil de seguir e solucionar problemas, pois suas regras de negócios e a lógica de negócios serão amontoadodas juntas. Essa opção também não fornece muita reutilização.

##### <a name="execute-data-flows-serially"></a>Executar fluxos de dados em série

Se você executar suas atividades de fluxo de dados em série no pipeline e tiver definido um TTL na configuração de Azure IR, o ADF reutilizará os recursos de computação (VMs) resultando em tempos de execução subsequentes mais rápidos. Você ainda receberá um novo contexto do Spark para cada execução.

Dessas três opções, provavelmente levará o tempo mais longo para ser executado de ponta a ponta. Mas ele fornece uma separação limpa das operações lógicas em cada etapa do fluxo de dados.

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as configurações específicas para a transformação selecionada no momento. Se nenhuma transformação for selecionada, ela mostrará o fluxo de dados. Na configuração geral do fluxo de dados, você pode editar o nome e a descrição na guia **geral** ou adicionar parâmetros por meio da guia **parâmetros** . Para obter mais informações, consulte [mapeando parâmetros de fluxo de dados](parameters-data-flow.md).

Cada transformação tem pelo menos quatro guias de configuração.

#### <a name="transformation-settings"></a>Configurações de transformação

A primeira guia em cada painel de configuração de transformação contém as configurações específicas para essa transformação. Para obter mais informações, consulte a página de documentação da transformação.

![Guia Configurações de origem](media/data-flow/source1.png "Guia Configurações de origem")

#### <a name="optimize"></a>Otimizar

A guia **otimizar** contém configurações para configurar esquemas de particionamento.

![Optimize](media/data-flow/optimize1.png "Otimizar")

A configuração padrão é **usar particionamento atual**, o que instrui Azure data Factory a usar o esquema de particionamento nativo para fluxos de dados em execução no Spark. Na maioria dos cenários, recomendamos essa configuração.

Há instâncias em que você talvez queira ajustar o particionamento. Por exemplo, se você quiser gerar suas transformações em um único arquivo no Lake, selecione **partição única** em uma transformação coletor.

Outro caso em que você talvez queira controlar os esquemas de particionamento é otimizar o desempenho. Ajustar o particionamento fornece controle sobre a distribuição de seus dados em nós de computação e otimizações de localidade de dados que podem ter efeitos positivos e negativos no desempenho geral do fluxo de dados. Para obter mais informações, consulte o [Guia de desempenho do fluxo de dados](concepts-data-flow-performance.md).

Para alterar o particionamento em qualquer transformação, selecione a guia **otimizar** e selecione o botão de opção **definir particionamento** . Em seguida, será apresentada uma série de opções de particionamento. O melhor método de particionamento será diferente com base em seus volumes de dados, chaves candidatas, valores nulos e cardinalidade. 

Uma prática recomendada é começar com o particionamento padrão e, em seguida, tentar diferentes opções de particionamento. Você pode testar usando execuções de depuração de pipeline e exibir o tempo de execução e o uso de partição em cada Agrupamento de transformação na exibição monitoramento. Para obter mais informações, consulte [monitorando fluxos de dados](concepts-data-flow-monitoring.md).

As seguintes opções de particionamento estão disponíveis.

##### <a name="round-robin"></a>Round Robin 

Round Robin é uma partição simples que distribui automaticamente os dados igualmente entre as partições. Use round robin quando você não tiver bons candidatos importantes para implementar uma estratégia sólida de particionamento inteligente. Você pode definir o número de partições físicas.

##### <a name="hash"></a>Hash

Azure Data Factory produzirá um hash de colunas para produzir partições uniformes, de modo que as linhas com valores semelhantes se enquadrarão na mesma partição. Ao usar a opção de hash, teste a possível distorção de partição. Você pode definir o número de partições físicas.

##### <a name="dynamic-range"></a>Intervalo dinâmico

O intervalo dinâmico usará intervalos dinâmicos do Spark com base nas colunas ou expressões que você fornecer. Você pode definir o número de partições físicas. 

##### <a name="fixed-range"></a>Intervalo fixo

Crie uma expressão que forneça um intervalo fixo para valores em suas colunas de dados particionados. Para evitar a distorção de partição, você deve ter uma boa compreensão dos seus dados antes de usar essa opção. Os valores inseridos para a expressão serão usados como parte de uma função de partição. Você pode definir o número de partições físicas.

##### <a name="key"></a>Chave

Se você tiver uma boa compreensão da cardinalidade de seus dados, o particionamento de chave poderá ser uma boa estratégia. O particionamento de chave criará partições para cada valor exclusivo em sua coluna. Você não pode definir o número de partições porque o número será baseado em valores exclusivos nos dados.

#### <a name="inspect"></a>Inspecionar

A guia **inspecionar** fornece uma exibição dos metadados do fluxo de dados que você está transformando. Você pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordenação de colunas e as referências de coluna. **Inspecionar** é uma exibição somente leitura de seus metadados. Você não precisa ter o modo de depuração habilitado para ver os metadados no painel **inspecionar** .

![Quanto](media/data-flow/inspect1.png "Inspecionar")

Ao alterar a forma de seus dados por meio de transformações, você verá o fluxo de alterações de metadados no painel **inspecionar** . Se não houver um esquema definido em sua transformação de origem, os metadados não estarão visíveis no painel **inspecionar** . A falta de metadados é comum em cenários de descompasso de esquema.

#### <a name="data-preview"></a>Pré-visualização dos dados

Se o modo de depuração estiver ativado, a guia **visualização de dados** fornecerá um instantâneo interativo dos dados em cada transformação. Para obter mais informações, consulte [visualização de dados em modo de depuração](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como salvar e validar. Você também pode alternar entre modos de gráfico e de configuração usando os botões **Mostrar grafo** e **ocultar grafo** .

![Ocultar grafo](media/data-flow/hideg.png "Ocultar grafo")

Se você ocultar o grafo, poderá navegar pelos nós de transformação posteriormente por meio dos botões **anterior** e **próximo** .

![Botões anterior e próximo](media/data-flow/showhide.png "botões anterior e próximo")

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma [transformação de origem](data-flow-source.md).
* Saiba como criar fluxos de dados no [modo de depuração](concepts-data-flow-debug-mode.md).
