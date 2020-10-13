---
title: Transformação de fonte no fluxo de dados de mapeamento
description: Saiba como configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/27/2020
ms.openlocfilehash: 22f524ed4f01ad5b3be110008a82cd31c5c476c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827845"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformação de fonte no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma transformação de fonte configura a sua fonte de dados para o fluxo de dados. Ao desenhar fluxos de dados, o seu primeiro passo será sempre configurar uma transformação de fonte. Para adicionar uma fonte, clique na caixa **Add Source** na tela de fluxo de dados.

Cada fluxo de dados requer pelo menos uma transformação de origem, mas pode adicionar o número de fontes necessárias para completar as suas transformações de dados. Pode juntar-se a essas fontes juntamente com uma junção, uma procura ou uma transformação sindical.

Cada transformação de origem está associada a exatamente um conjunto de dados ou serviço ligado. O conjunto de dados define a forma e a localização dos dados a que pretende escrever ou ler. Se utilizar um conjunto de dados baseado em ficheiros, pode utilizar wildcards e listas de ficheiros na sua fonte para trabalhar com mais de um ficheiro de cada vez.

## <a name="inline-datasets"></a>Conjuntos de dados inline

A primeira decisão que tomar ao criar uma transformação de fonte é se a sua informação de origem é definida dentro de um objeto de conjunto de dados ou dentro da transformação da fonte. A maioria dos formatos só estão disponíveis num ou noutro. Por favor, consulte o documento de conector apropriado para aprender a usar um conector específico.

Quando um formato é suportado tanto para inline como para um objeto conjunto de dados, existem benefícios para ambos. Os objetos do conjunto de dados são entidades reutilizáveis que podem ser alavancadas em outros fluxos de dados e atividades como copy. Estes são especialmente úteis quando se utiliza um esquema endurecido. Os conjuntos de dados não são baseados em Spark e ocasionalmente poderá ser necessário anular determinadas definições ou projeção de esquemas na transformação da fonte.

Os conjuntos de dados inline são recomendados quando se utilizam esquemas flexíveis, instâncias de origem pontuais ou fontes parametrizadas. Se a sua fonte for fortemente parametrizada, os conjuntos de dados em linha permitem-lhe não criar um objeto "manequim". Os conjuntos de dados inline são baseados em faíscas e as suas propriedades são nativas do fluxo de dados.

Para utilizar um conjunto de dados inline, selecione o formato pretendido no seletor **de tipo Fonte.** Em vez de selecionar um conjunto de dados de origem, selecione o serviço ligado ao que pretende ligar.

![Conjunto de dados inline](media/data-flow/inline-selector.png "Conjunto de dados inline")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Tipos de origem suportados

Mapeamento do Fluxo de Dados segue uma abordagem de extrato, carga, transformação (ELT) e trabalha com conjuntos de dados de *encenação* que estão todos em Azure. Atualmente, os seguintes conjuntos de dados podem ser utilizados numa transformação de fonte:

| Conector | Formato | Conjunto de dados/inline |
| --------- | ------ | -------------- |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (pré-visualização)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (Armazenamento do Azure Data Lake Gen2) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Modelo de dados comum (pré-visualização)](format-common-data-model.md#source-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (pré-visualização)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

As definições específicas destes conectores estão localizadas no separador **opções Source.** Os exemplos de scripts de fluxo de informação e de fluxo de dados nestas definições estão localizados na documentação do conector. 

O Azure Data Factory tem acesso a mais de [90 conectores nativos](connector-overview.md). Para incluir dados dessas outras fontes no seu fluxo de dados, utilize a Atividade de Cópia para carregar esses dados numa das áreas de preparação suportadas.

## <a name="source-settings"></a>Definições de origem

Depois de ter adicionado uma fonte, configufique através do separador **Definições de Origem.** Aqui pode escolher ou criar o conjunto de dados onde os seus pontos de origem. Também pode selecionar opções de esquema e amostragem para os seus dados. 

Os valores de desenvolvimento dos parâmetros do conjunto de dados podem ser configurados nas [definições de Debug](concepts-data-flow-debug-mode.md) (requer que o modo Debug seja ligado).

![Separador de definições de origem](media/data-flow/source1.png "Separador de definições de origem")

**Nome do fluxo de saída:** O nome da transformação da fonte.

**Tipo de origem:** Escolha se pretende utilizar um conjunto de dados inline ou um objeto de conjunto de dados existente.

**Ligação de teste:** Teste se o serviço de faíscas do fluxo de dados pode ou não ligar-se com sucesso ao serviço ligado utilizado no seu conjunto de dados de origem. O modo Debug deve estar ligado para que esta função seja ativada.

**Schema Drift:** [Schema Drift](concepts-data-flow-schema-drift.md) é a capacidade da fábrica de dados de lidar de forma nativa com esquemas flexíveis nos fluxos de dados sem precisar de definir explicitamente as alterações de colunas.

* Verifique a caixa **de deriva de esquema de permitir** se as colunas de origem mudarem frequentemente. Esta definição permite que todos os campos de origem que entram fluam através das transformações para a pia.

* A escolha dos tipos de **colunas derivadas infer** irá instruir a fábrica de dados a detetar e definir tipos de dados para cada nova coluna descoberta. Com esta função desligada, todas as colunas à deriva serão de tipo de cadeia.

**Validar esquema:** Se o esquema de validação for selecionado, o fluxo de dados não será executado se os dados de origem recebidas não corresponderem ao esquema definido do conjunto de dados.

**Contagem de linhas:** O campo de contagem de linhas de salto especifica quantas linhas devem ignorar no início do conjunto de dados.

**Amostragem:** Ativar a amostragem para limitar o número de linhas da sua fonte. Utilize esta definição quando testar ou recolher dados da sua fonte para fins de depuragem.

Para validar a sua fonte está configurada corretamente, ligue o modo de depurar e reveja uma pré-visualização de dados. Para mais informações, consulte [o modo Debug.](concepts-data-flow-debug-mode.md)

> [!NOTE]
> Quando o modo de depurador é ligado, a configuração do limite de linha nas definições de depurador substituirá a definição de amostragem na fonte durante a pré-visualização dos dados.

## <a name="source-options"></a>Opções de origem

O separador opções de origem contém definições específicas do conector e do formato escolhidos. Para mais informações e exemplos, consulte a documentação do [conector](#supported-sources)relevante.

## <a name="projection"></a>Projeção

Tal como os esquemas em conjuntos de dados, a projeção numa fonte define as colunas de dados, tipos e formatos a partir dos dados de origem. Para a maioria dos tipos de conjuntos de dados, como SQL e Parquet, a projeção numa fonte é fixada para refletir o esquema definido num conjunto de dados. Quando os seus ficheiros de origem não são fortemente digitados (por exemplo, ficheiros csv planos em vez de ficheiros Parquet), pode definir os tipos de dados para cada campo na transformação da fonte.

![Definições no separador Projeção](media/data-flow/source3.png "Projeção")

Se o seu ficheiro de texto não tiver um esquema definido, selecione **Detetar o tipo de dados** para que a Data Factory recolha e infera os tipos de dados. Selecione **Definir o formato predefinido** para fazer adetectação automática dos formatos de dados predefinidos.

**O esquema de reset** repõe a projeção para o que é definido no conjunto de dados referenciado.

Pode modificar os tipos de dados de coluna numa transformação de coluna derivada abaixo. Utilize uma transformação selecionada para modificar os nomes das colunas.

### <a name="import-schema"></a>Esquema de importação

O botão **Desembargador** de Importação no **separador Projeção** permite-lhe utilizar um cluster de depuração ativo para criar uma projeção de esquema. Disponível em todos os tipos de origem, a importação do esquema aqui irá sobrepor-se à projeção definida no conjunto de dados. O objeto do conjunto de dados não será alterado.

Isto é útil em conjuntos de dados como Avro e Azure Cosmos DB que suportam estruturas de dados complexas não requerem definições de esquemas no conjunto de dados. Para conjuntos de dados inline, esta é a única maneira de referenciar metadados de coluna sem deriva de esquema.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação da fonte

O **separador Otimize** permite a edição de informações de partição em cada passo de transformação. Na maioria dos casos, a partilha atual de utilização irá **otimizar** para a estrutura de partição ideal para uma fonte.

Se estiver a ler a partir de uma fonte de Base de Dados Azure SQL, a partição de **origem** personalizada provavelmente lerá os dados mais rápido. A ADF irá ler grandes consultas fazendo ligações à sua base de dados em paralelo. Esta partição de fonte pode ser feita numa coluna ou utilizando uma consulta.

![Definições de partição de origem](media/data-flow/sourcepart3.png "partição")

Para obter mais informações sobre a otimização dentro do fluxo de dados de mapeamento, consulte o [separador Otimize](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passos seguintes

Comece a construir o seu fluxo de dados com uma [transformação de colunas derivadas](data-flow-derived-column.md) e uma [transformação selecionada.](data-flow-select.md)
