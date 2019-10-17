---
title: Mapeando fluxos de dados em Azure Data Factory | Microsoft Docs
description: Uma visão geral do mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 37fec388acda78f3d13c8e85ddddf780ad099d69
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388002"
---
# <a name="what-are-mapping-data-flows"></a>O que são os fluxos de dados de mapeamento?

O mapeamento de fluxos de dados são transformações de dados visualmente projetadas em Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro de Azure Data Factory pipelines usando clusters Spark expandidos. As atividades de fluxo de dados podem ser operadas por meio de recursos existentes de agendamento de Data Factory, controle, fluxo e monitoramento.

O mapeamento de fluxos de dados fornece uma experiência totalmente visual sem a necessidade de codificação. Seus fluxos de dados serão executados em seu próprio cluster de execução para processamento de dados expandido. Azure Data Factory lida com toda a tradução de código, a otimização de caminho e a execução de seus trabalhos de fluxo de dados.

## <a name="getting-started"></a>Introdução

Para criar um fluxo de dados, clique no sinal de adição em recursos de fábrica. 

![novo fluxo de dados](media/data-flow/newdataflow2.png "novo fluxo de dados")

Isso levará você para a tela de fluxo de dados, na qual você pode criar a lógica de transformação. Clique na caixa ' Adicionar origem ' para começar a configurar sua transformação de origem. Para obter mais informações, consulte [Source Transformation](data-flow-source.md).

## <a name="data-flow-canvas"></a>Tela de fluxo de dados

A tela de fluxo de dados é separada em três partes: a barra superior, o grafo e o painel de configuração. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Graph

O grafo exibe o fluxo de transformação. Ele mostra a linhagem dos dados de origem conforme eles fluem em um ou mais coletores. Para adicionar uma nova origem, clique na caixa ' Adicionar fonte '. Para adicionar uma nova transformação, clique no sinal de mais na parte inferior direita de uma transformação existente.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>Painel de configuração

O painel de configuração mostra as configurações específicas para a transformação selecionada no momento ou, se nenhuma transformação for selecionada, o fluxo de dados. Na configuração geral do fluxo de dados, você pode editar o nome e a descrição na guia **geral** ou adicionar parâmetros por meio da guia **parâmetros** . Para obter mais informações, consulte [mapeando parâmetros de fluxo de dados](parameters-data-flow.md).

Cada transformação tem pelo menos quatro guias de configuração:

#### <a name="transformation-settings"></a>Configurações de transformação

A primeira guia em cada painel de configuração de transformação contém as configurações específicas para essa transformação. Para obter mais informações, consulte a página de documentação da transformação.

![Guia Configurações de origem](media/data-flow/source1.png "Guia Configurações de origem")

#### <a name="optimize"></a>Otimizar

A guia _otimizar_ contém configurações para configurar esquemas de particionamento.

![Optimize](media/data-flow/optimize1.png "Otimizar")

A configuração padrão é "usar particionamento atual", que instrui Azure Data Factory a usar o esquema de particionamento nativo para fluxos de dados em execução no Spark. Na maioria dos cenários, essa configuração é a abordagem recomendada.

Há instâncias em que você pode desejar ajustar o particionamento. Por exemplo, se você quiser gerar suas transformações em um único arquivo no Lake, escolha "partição única" em uma transformação do coletor.

Outro caso em que você talvez queira controlar os esquemas de particionamento é otimizar o desempenho. Ajustar o particionamento fornece controle sobre a distribuição de seus dados em nós de computação e otimizações de localidade de dados que podem ter efeitos positivos e negativos no desempenho geral do fluxo de dados. Para obter mais informações, consulte o [Guia de desempenho do fluxo de dados](concepts-data-flow-performance.md).

Para alterar o particionamento em qualquer transformação, clique na guia otimizar e selecione o botão de opção "definir particionamento". Em seguida, será apresentada uma série de opções de particionamento. O melhor método de particionamento será diferente com base em seus volumes de dados, chaves candidatas, valores nulos e cardinalidade. Uma prática recomendada é começar com o particionamento padrão e, em seguida, tentar diferentes opções de particionamento. Você pode testar usando execuções de depuração de pipeline e exibir o uso de partição e o tempo de execução em cada Agrupamento de transformação na exibição monitoramento. Para obter mais informações, consulte [monitorando fluxos de dados](concepts-data-flow-monitoring.md).

Abaixo estão as opções de particionamento disponíveis.

##### <a name="round-robin"></a>Round Robin 

Round Robin é uma partição simples que distribui automaticamente os dados igualmente entre as partições. Use Round Robin quando você não tiver bons candidatos importantes para implementar uma estratégia sólida de particionamento inteligente. Você pode definir o número de partições físicas.

##### <a name="hash"></a>Hash

Azure Data Factory produzirá um hash de colunas para produzir partições uniformes, de modo que as linhas com valores semelhantes se enquadrarão na mesma partição. Ao usar a opção de hash, teste a possível distorção de partição. Você pode definir o número de partições físicas.

##### <a name="dynamic-range"></a>Intervalo dinâmico

O intervalo dinâmico usará intervalos dinâmicos do Spark com base nas colunas ou expressões que você fornecer. Você pode definir o número de partições físicas. 

##### <a name="fixed-range"></a>Intervalo fixo

Crie uma expressão que forneça um intervalo fixo para valores em suas colunas de dados particionados. Você deve ter uma boa compreensão dos seus dados antes de usar essa opção para evitar a distorção de partição. Os valores inseridos para a expressão serão usados como parte de uma função de partição. Você pode definir o número de partições físicas.

##### <a name="key"></a>Chave

Se você tem uma boa compreensão da cardinalidade de seus dados, o particionamento de chave pode ser uma boa estratégia de partição. O particionamento de chave criará partições para cada valor exclusivo em sua coluna. Você não pode definir o número de partições porque o número será baseado em valores exclusivos nos dados.

#### <a name="inspect"></a>Quanto

A guia _inspecionar_ fornece uma exibição dos metadados do fluxo de dados que você está transformando. Você pode ver as contagens de colunas, as colunas alteradas, as colunas adicionadas, os tipos de dados, a ordenação de colunas e as referências de coluna. Inspecionar é uma exibição somente leitura de seus metadados. Você não precisa ter o modo de depuração habilitado para ver os metadados no painel inspecionar.

![Quanto](media/data-flow/inspect1.png "Quanto")

Ao alterar a forma de seus dados por meio de transformações, você verá o fluxo de alterações de metadados por meio do painel inspecionar. Se não houver um esquema definido em sua transformação de origem, os metadados não estarão visíveis no painel inspecionar. A falta de metadados é comum em cenários de descompasso de esquema.

#### <a name="data-preview"></a>Visualização de dados

Se o modo de depuração estiver ativado, a guia _visualização de dados_ fornecerá um instantâneo interativo dos dados em cada transformação. Para obter mais informações, consulte [visualização de dados em modo de depuração](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superior

A barra superior contém ações que afetam todo o fluxo de dados, como salvar e validar. Você também pode alternar entre modos de gráfico e de configuração usando os botões **Mostrar grafo** e **ocultar grafo** .

![Ocultar grafo](media/data-flow/hideg.png "Ocultar grafo")

Se você ocultar o grafo, poderá navegar pelos nós de transformação posteriormente por meio dos botões **anterior** e **próximo** .

![Navegue até](media/data-flow/showhide.png "Navegue até")

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma [transformação de origem](data-flow-source.md)
* Saiba como criar fluxos de dados no [modo de depuração](concepts-data-flow-debug-mode.md)
