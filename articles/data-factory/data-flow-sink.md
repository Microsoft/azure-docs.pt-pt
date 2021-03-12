---
title: Transformação do sumidouro no fluxo de dados de mapeamento
description: Aprenda a configurar uma transformação de pia no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 5548d82326ec4ac2306e2c8945bedc20236a4e54
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009356"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformação do sumidouro no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Depois de terminar de transformar os seus dados, escreva-os numa loja de destino utilizando a transformação do lavatório. Cada fluxo de dados requer pelo menos uma transformação de sumidouro, mas você pode escrever para o número de pias necessárias para completar o seu fluxo de transformação. Para escrever para lavatórios adicionais, crie novos fluxos através de novos ramos e divisões condicionais.

Cada transformação do lavatório está associada a exatamente um objeto de conjunto de dados Azure Data Factory ou serviço ligado. A transformação do lavatório determina a forma e a localização dos dados a que pretende escrever.

## <a name="inline-datasets"></a>Conjuntos de dados inline

Quando criar uma transformação de pia, escolha se a informação da pia é definida dentro de um objeto de conjunto de dados ou dentro da transformação do lavatório. A maioria dos formatos estão disponíveis em apenas um ou outro. Para aprender a utilizar um conector específico, consulte o documento de conector apropriado.

Quando um formato é suportado tanto para inline como para um objeto conjunto de dados, existem benefícios para ambos. Os objetos do conjunto de dados são entidades reutilizáveis que podem ser usadas em outros fluxos de dados e atividades como copy. Estas entidades reutilizáveis são especialmente úteis quando se usa um esquema endurecido. Os conjuntos de dados não são baseados em Spark. Ocasionalmente, poderá ser necessário anular determinadas definições ou projeção de esquemas na transformação do lavatório.

Recomenda-se conjuntos de dados em linha quando se utilizam esquemas flexíveis, instâncias de pia únicas ou pias parametrizadas. Se a pia for fortemente parametrizada, os conjuntos de dados inline permitem-lhe não criar um objeto "manequim". Os conjuntos de dados inline são baseados em Spark, e as suas propriedades são nativas do fluxo de dados.

Para utilizar um conjunto de dados inline, selecione o formato que pretende no seletor **do tipo Sink.** Em vez de selecionar um conjunto de dados de lavatório, selecione o serviço ligado ao que pretende ligar.

![Screenshot que mostra Inline selecionado.](media/data-flow/inline-selector.png "Screenshot que mostra Inline selecionado.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Tipos de pias suportados

O fluxo de dados de mapeamento segue uma abordagem de extrato, carga e transformação (ELT) e trabalha com conjuntos de dados de *encenação* que estão todos em Azure. Atualmente, os seguintes conjuntos de dados podem ser usados numa transformação de fonte.

| Conector | Formato | Conjunto de dados/inline |
| --------- | ------ | -------------- |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (Armazenamento do Azure Data Lake Gen2) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Common Data Service](format-common-data-model.md#sink-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Base de Dados do Azure para MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Base de Dados do Azure para PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Caso Gerido (pré-visualização)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

As definições específicas destes conectores estão localizadas no **separador Definições.** Os exemplos de scripts de fluxo de informação e de fluxo de dados nestas definições estão localizados na documentação do conector.

A Azure Data Factory tem acesso a mais de [90 conectores nativos.](connector-overview.md) Para escrever dados para as outras fontes do seu fluxo de dados, utilize a Atividade de Cópia para carregar esses dados a partir de um lavatório suportado.

## <a name="sink-settings"></a>Configurações do lavatório

Depois de ter adicionado uma pia, configuure-se através da conta **da Pia.** Aqui pode escolher ou criar o conjunto de dados para o qual a pia escreve. Os valores de desenvolvimento dos parâmetros do conjunto de dados podem ser configurados nas [definições de Debug](concepts-data-flow-debug-mode.md). (O modo Debug deve ser ligado.)

O vídeo seguinte explica uma série de diferentes opções de pia para tipos de ficheiros delimitados por texto.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Screenshot que mostra as definições da Pia.](media/data-flow/sink-settings.png "Screenshot que mostra as definições da Pia.")

**Deriva de Schema**: A deriva de [Schema](concepts-data-flow-schema-drift.md) é a capacidade da Data Factory de lidar de forma nativa com esquemas flexíveis nos fluxos de dados sem precisar de definir explicitamente as alterações de colunas. Ativar **Permitir que o schema drift** escreva colunas adicionais em cima do que está definido no esquema de dados do lavatório.

**Validar esquema**: Se for selecionado um esquema validado, o fluxo de dados falhará se alguma coluna do esquema de origem de entrada não for encontrada na projeção da fonte ou se os tipos de dados não coincidirem. Utilize esta definição para impor que os dados de origem satisfaçam o contrato da sua projeção definida. É útil em cenários de origem de base de dados sinalizar que nomes ou tipos de colunas mudaram.

## <a name="cache-sink"></a>Pia cache

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

Um *lavatório de cache* é quando um fluxo de dados escreve dados na cache Spark em vez de uma loja de dados. Ao mapear fluxos de dados, pode referenciar estes dados dentro do mesmo fluxo muitas vezes usando uma *procura de cache*. Isto é útil quando se pretende fazer referência aos dados como parte de uma expressão, mas não quer juntar explicitamente as colunas a ela. Exemplos comuns em que um lavatório de cache pode ajudar estão a procurar um valor máximo numa loja de dados e a combinar códigos de erro para uma base de dados de mensagens de erro. 

Para escrever para uma pia de cache, adicione uma transformação de pia e selecione **Cache** como o tipo de pia. Ao contrário de outros tipos de pias, não precisa de selecionar um conjunto de dados ou um serviço ligado porque não está a escrever para uma loja externa. 

![Selecione pia de cache](media/data-flow/select-cache-sink.png "Selecione pia de cache")

Nas definições da pia, pode especificar opcionalmente as colunas-chave da pia de cache. Estas são utilizadas como condições de correspondência quando se utiliza a `lookup()` função numa procura de cache. Se especificar colunas-chave, não pode utilizar a `outputs()` função numa procura de cache. Para saber mais sobre a sintaxe de procuração de cache, consulte [as pesquisas em cache](concepts-data-flow-expression-builder.md#cached-lookup).

![Cache lava as colunas-chave](media/data-flow/cache-sink-key-columns.png "Cache lava as colunas-chave")

Por exemplo, se eu especificar uma única coluna-chave de `column1` um lavatório chamado `cacheExample` , a chamada teria um parâmetro que `cacheExample#lookup()` especificaria qual a linha na pia da cache para combinar. A função produz uma única coluna complexa com subcolumns para cada coluna mapeada.

> [!NOTE]
> Um lavatório de cache deve estar num fluxo de dados completamente independente de qualquer transformação que o remeta através de uma procura de cache. Um lavatório também deve ser escrito o primeiro lavatório. 

## <a name="field-mapping"></a>Mapeamento de campos

Semelhante a uma transformação selecionada, no **separador mapeamento** da pia, pode decidir quais colunas de entrada serão escritas. Por predefinição, todas as colunas de entrada, incluindo colunas derivadas, estão mapeadas. Este comportamento é conhecido como *automapping*.

Quando desativar o automatismo, pode adicionar mapeamentos fixos baseados em colunas ou mapeamentos baseados em regras. Com mapeamentos baseados em regras, pode escrever expressões com correspondência de padrões. Mapas de mapeamento fixos nomes de colunas lógicas e físicas. Para obter mais informações sobre o mapeamento baseado em [regras, consulte os padrões da Coluna no fluxo de dados de mapeamento](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Pedido de pia personalizado

Por predefinição, os dados são escritos a vários lavatórios numa ordem não determinística. O motor de execução escreve dados em paralelo à medida que a lógica de transformação é concluída, e o pedido de pia pode variar cada corrida. Para especificar um pedido de pia exato, ative o pedido de **lavatório personalizado** no separador **Geral** do fluxo de dados. Quando ativados, os lavatórios são escritos sequencialmente em ordem crescente.

![Screenshot que mostra pedido de pia personalizada.](media/data-flow/custom-sink-ordering.png "Screenshot que mostra pedido de pia personalizada.")

> [!NOTE]
> Ao utilizar [pesquisas em cache,](./concepts-data-flow-expression-builder.md#cached-lookup)certifique-se de que o seu pedido de pia tem os lavatórios em cache definidos para 1, o mais baixo (ou primeiro) na encomenda.

![Pedido de pia personalizado](media/data-flow/cache-2.png "Pedido de pia personalizado")

### <a name="sink-groups"></a>Grupos de afundanços

Pode agrupar-se juntando aplicando o mesmo número de encomenda para uma série de pias. A ADF tratará esses lavatórios como grupos que podem executar em paralelo. As opções para a execução paralela surgirão na atividade de fluxo de dados do gasoduto.

## <a name="error-row-handling"></a>Processamento da linha de erro

Ao escrever para bases de dados, certas linhas de dados podem falhar devido a restrições definidas pelo destino. Por predefinição, um fluxo de dados falhará no primeiro erro que obtém. Em certos conectores, pode optar por Continuar por **erro** que permite que o fluxo de dados seja concluído mesmo que as linhas individuais tenham erros. Atualmente, esta capacidade só está disponível na Base de Dados Azure SQL. Para obter mais informações, consulte [o manuseamento de linhas de erro no Azure SQL DB](connector-azure-sql-database.md#error-row-handling).

Abaixo está um tutorial de vídeo sobre como usar o manuseamento de linha de erro de base de dados automaticamente na sua transformação de pia.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Pré-visualização de dados na pia

Ao obter uma pré-visualização de dados num cluster de depuração, nenhum dado será escrito na sua pia. Uma imagem do aspeto dos dados será devolvida, mas nada será escrito para o seu destino. Para testar os dados de escrita na pia, coloque um depuração de gasoduto a partir da tela do gasoduto.

## <a name="next-steps"></a>Passos seguintes

Agora que criou o seu fluxo de dados, adicione uma [atividade de fluxo de dados ao seu pipeline](concepts-data-flow-overview.md).