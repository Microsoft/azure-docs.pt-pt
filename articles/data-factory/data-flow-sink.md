---
title: Transformação do sumidouro no fluxo de dados de mapeamento
description: Aprenda a configurar uma transformação de pia no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/27/2020
ms.openlocfilehash: 06d70012756694dca1fad8fa90db0293bb106bf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828142"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformação do sumidouro no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Depois de terminar de transformar os seus dados, escreva-os numa loja de destino utilizando a transformação da pia. Cada fluxo de dados requer pelo menos uma transformação de sumidouro, mas você pode escrever para o número de pias necessárias para completar o seu fluxo de transformação. Para escrever para lavatórios adicionais, crie novos fluxos através de novos ramos e divisões condicionais.

Cada transformação do lavatório está associada a exatamente um objeto de conjunto de dados Azure Data Factory ou serviço ligado. A transformação do lavatório determina a forma e a localização dos dados a que pretende escrever.

## <a name="inline-datasets"></a>Conjuntos de dados inline

Ao criar uma transformação de pia, escolha se a informação do lavatório é definida dentro de um objeto de conjunto de dados ou dentro da transformação do lavatório. A maioria dos formatos só estão disponíveis num ou noutro. Por favor, consulte o documento de conector apropriado para aprender a usar um conector específico.

Quando um formato é suportado tanto para inline como para um objeto conjunto de dados, existem benefícios para ambos. Os objetos do conjunto de dados são entidades reutilizáveis que podem ser alavancadas em outros fluxos de dados e atividades como copy. Estes são especialmente úteis quando se utiliza um esquema endurecido. Os conjuntos de dados não são baseados em Spark e ocasionalmente poderá ser necessário anular determinadas definições ou projeção de esquemas na transformação do lavatório.

Os conjuntos de dados inline são recomendados quando se utilizam esquemas flexíveis, instâncias de pia únicas ou pias parametrizadas. Se a pia for fortemente parametrizada, os conjuntos de dados em linha permitem-lhe não criar um objeto "manequim". Os conjuntos de dados inline são baseados em faíscas e as suas propriedades são nativas do fluxo de dados.

Para utilizar um conjunto de dados inline, selecione o formato pretendido no seletor **do tipo Sink.** Em vez de selecionar um conjunto de dados de lavatório, selecione o serviço ligado ao que pretende ligar.

![Conjunto de dados inline](media/data-flow/inline-selector.png "Conjunto de dados inline")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Tipos de pias suportados

Mapeamento do Fluxo de Dados segue uma abordagem de extrato, carga, transformação (ELT) e trabalha com conjuntos de dados de *encenação* que estão todos em Azure. Atualmente, os seguintes conjuntos de dados podem ser utilizados numa transformação de fonte:

| Conector | Formato | Conjunto de dados/inline |
| --------- | ------ | -------------- |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (pré-visualização)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (Armazenamento do Azure Data Lake Gen2) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (pré-visualização)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Modelo de dados comum (pré-visualização)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

As definições específicas destes conectores estão localizadas no **separador Definições.** Os exemplos de scripts de fluxo de informação e de fluxo de dados nestas definições estão localizados na documentação do conector. 

O Azure Data Factory tem acesso a mais de [90 conectores nativos](connector-overview.md). Para escrever dados para as outras fontes do seu fluxo de dados, utilize a Atividade de Cópia para carregar esses dados a partir de um lavatório suportado.

## <a name="sink-settings"></a>Configurações do lavatório

Depois de ter adicionado uma pia, configuure-se através da **lingueta De Sumidouro.** Aqui pode escolher ou criar o conjunto de dados para o qual a pia escreve. Os valores de desenvolvimento dos parâmetros do conjunto de dados podem ser configurados nas [definições de Debug](concepts-data-flow-debug-mode.md) (requer que o modo Debug seja ligado).

Abaixo está um vídeo que explica uma série de diferentes opções de Sink para tipos de ficheiros delimitados por texto:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Configurações do lavatório](media/data-flow/sink-settings.png "Definições de pia")

**Schema Drift:** [Schema Drift](concepts-data-flow-schema-drift.md) é a capacidade da fábrica de dados de lidar de forma nativa com esquemas flexíveis nos fluxos de dados sem precisar de definir explicitamente as alterações de colunas. Ativar **Permitir que o schema drift** escreva colunas adicionais em cima do que está definido no esquema de dados do lavatório.

**Validar esquema:** Se for selecionado um esquema validado, o fluxo de dados falhará se alguma coluna do esquema de origem de entrada não for encontrada na projeção da fonte ou se os tipos de dados não corresponderem. Utilize esta definição para impor que os dados de origem satisfaçam o contrato da sua projeção definida. É muito útil em cenários de origem de base de dados sinalizar que nomes ou tipos de colunas mudaram.

## <a name="field-mapping"></a>Mapeamento de campos

Semelhante a uma transformação Select, no **separador mapeamento** da pia, pode decidir quais colunas de entrada serão escritas. Por predefinição, todas as colunas de entrada, incluindo colunas derivadas, estão mapeadas. Isto é conhecido como **auto-mapeamento.**

Ao desligar o mapeamento automático, terá a opção de adicionar mapeamentos fixos baseados em colunas ou mapeamentos baseados em regras. Os mapeamentos baseados em regras permitem-lhe escrever expressões com correspondência de padrões enquanto o mapeamento fixo mapeia nomes lógicos e de colunas físicas. Para obter mais informações sobre o mapeamento baseado em [regras, consulte os padrões das colunas no fluxo de dados de mapeamento](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Pedido de pia personalizado

Por predefinição, os dados são escritos a vários lavatórios numa ordem não determinística. O motor de execução escreverá dados em paralelo à medida que a lógica de transformação estiver concluída e a ordem do lavatório pode variar cada execução. Para especificar e especificar o pedido de lavatório exato, ative o pedido de **lavatório personalizado** no separador geral do fluxo de dados. Quando ativados, os lavatórios serão escritos sequencialmente em ordem crescente.

![Pedido de pia personalizado](media/data-flow/custom-sink-ordering.png "Pedido de pia personalizado")

## <a name="data-preview-in-sink"></a>Pré-visualização de dados na pia

Ao obter uma pré-visualização de dados num cluster de depuração, nenhum dado será escrito na sua pia. Uma imagem do aspeto dos dados será devolvida, mas nada será escrito para o seu destino. Para testar os dados de escrita na pia, coloque um depuração de gasoduto a partir da tela do gasoduto.

## <a name="next-steps"></a>Passos seguintes
Agora que criou o seu fluxo de dados, adicione uma [atividade de Fluxo de Dados ao seu pipeline](concepts-data-flow-overview.md).
