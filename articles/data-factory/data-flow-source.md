---
title: Transformação de fonte no fluxo de dados de mapeamento
description: Saiba como configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 80280702748040e12d1d3d048644e6a16c926256
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832386"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformação de fonte no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma transformação de fonte configura a sua fonte de dados para o fluxo de dados. Quando desenha fluxos de dados, o seu primeiro passo é sempre configurar uma transformação de fonte. Para adicionar uma fonte, selecione a caixa **Add Source** na tela de fluxo de dados.

Cada fluxo de dados requer pelo menos uma transformação de origem, mas pode adicionar o número de fontes necessárias para completar as suas transformações de dados. Pode juntar-se a essas fontes juntamente com uma junção, uma procura ou uma transformação sindical.

Cada transformação de origem está associada a exatamente um conjunto de dados ou serviço ligado. O conjunto de dados define a forma e a localização dos dados a que pretende escrever ou ler. Se utilizar um conjunto de dados baseado em ficheiros, pode utilizar wildcards e listas de ficheiros na sua fonte para trabalhar com mais de um ficheiro de cada vez.

## <a name="inline-datasets"></a>Conjuntos de dados inline

A primeira decisão que tomar quando cria uma transformação de fonte é se a sua informação de origem é definida dentro de um objeto de conjunto de dados ou dentro da transformação da fonte. A maioria dos formatos estão disponíveis em apenas um ou outro. Para aprender a utilizar um conector específico, consulte o documento de conector apropriado.

Quando um formato é suportado tanto para inline como para um objeto conjunto de dados, existem benefícios para ambos. Os objetos do conjunto de dados são entidades reutilizáveis que podem ser usadas em outros fluxos de dados e atividades como copy. Estas entidades reutilizáveis são especialmente úteis quando se usa um esquema endurecido. Os conjuntos de dados não são baseados em Spark. Ocasionalmente, poderá ser necessário anular determinadas definições ou projeção de esquemas na transformação da fonte.

Os conjuntos de dados inline são recomendados quando utiliza esquemas flexíveis, instâncias de origem única ou fontes parametrizadas. Se a sua fonte for fortemente parametrizada, os conjuntos de dados inline permitem-lhe não criar um objeto "manequim". Os conjuntos de dados inline são baseados em Spark, e as suas propriedades são nativas do fluxo de dados.

Para utilizar um conjunto de dados inline, selecione o formato que pretende no seletor **de tipo Fonte.** Em vez de selecionar um conjunto de dados de origem, selecione o serviço ligado ao que pretende ligar.

![Screenshot que mostra Inline selecionado.](media/data-flow/inline-selector.png "Screenshot que mostra Inline selecionado.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Tipos de origem suportados

O fluxo de dados de mapeamento segue uma abordagem de extrato, carga e transformação (ELT) e trabalha com conjuntos de dados de *encenação* que estão todos em Azure. Atualmente, os seguintes conjuntos de dados podem ser usados numa transformação de fonte.

| Conector | Formato | Conjunto de dados/inline |
| --------- | ------ | -------------- |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (pré-visualização)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (Armazenamento do Azure Data Lake Gen2) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Modelo de dados comum (pré-visualização)](format-common-data-model.md#source-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (pré-visualização)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Caso Gerido (pré-visualização)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ | 
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

As definições específicas destes conectores estão localizadas no separador **opções Source.** Os exemplos de scripts de fluxo de informação e de fluxo de dados nestas definições estão localizados na documentação do conector.

A Azure Data Factory tem acesso a mais de [90 conectores nativos.](connector-overview.md) Para incluir dados dessas outras fontes no seu fluxo de dados, utilize a Atividade de Cópia para carregar esses dados numa das áreas de preparação suportadas.

## <a name="source-settings"></a>Definições de origem

Depois de ter adicionado uma fonte, configufique através do **separador Definições De Origem.** Aqui pode escolher ou criar o conjunto de dados onde os seus pontos de origem. Também pode selecionar opções de esquema e amostragem para os seus dados.

Os valores de desenvolvimento dos parâmetros do conjunto de dados podem ser configurados nas [definições de depurrão](concepts-data-flow-debug-mode.md). (O modo Debug deve ser ligado.)

![Screenshot que mostra o separador Definições de Origem.](media/data-flow/source1.png "Screenshot que mostra o separador Definições de Origem.")

**Nome do fluxo de saída**: O nome da transformação da fonte.

**Tipo de origem**: Escolha se pretende utilizar um conjunto de dados inline ou um objeto de conjunto de dados existente.

**Ligação de teste**: Teste se o serviço Spark do fluxo de dados pode ou não ligar-se com sucesso ao serviço ligado utilizado no seu conjunto de dados de origem. O modo Debug deve estar ligado para que esta função seja ativada.

**Deriva de Schema**: A deriva de [Schema](concepts-data-flow-schema-drift.md) é a capacidade da Data Factory de lidar de forma nativa com esquemas flexíveis nos fluxos de dados sem precisar de definir explicitamente as alterações de colunas.

* Selecione a caixa de verificação **de deriva de esquema de esquema** se as colunas de origem mudarem frequentemente. Esta definição permite que todos os campos de origem que entram fluam através das transformações para a pia.

* Selecionar tipos de **colunas derivadas infer** instrui a Data Factory a detetar e definir tipos de dados para cada nova coluna descoberta. Com esta função desligada, todas as colunas à deriva serão de tipo de cadeia.

**Validar esquema:** Se **o esquema validado** for selecionado, o fluxo de dados não será executado se os dados de origem recebidas não corresponderem ao esquema definido do conjunto de dados.

**Contagem de linhas sem limites**: O campo **de contagem de linhas skip** especifica quantas linhas ignorar no início do conjunto de dados.

**Amostragem**: Permita que a **amostragem** limite o número de linhas da sua fonte. Utilize esta definição quando testar ou recolher dados da sua fonte para fins de depuragem.

Para validar a sua fonte está configurada corretamente, ligue o modo de depurar e reveja uma pré-visualização de dados. Para mais informações, consulte [o modo Debug.](concepts-data-flow-debug-mode.md)

> [!NOTE]
> Quando o modo de depurador é ligado, a configuração do limite de linha nas definições de depurador substituirá a definição de amostragem na fonte durante a pré-visualização dos dados.

## <a name="source-options"></a>Opções de origem

O separador **opções Source** contém definições específicas do conector e do formato escolhidos. Para obter mais informações e exemplos, consulte a documentação do [conector](#supported-sources)relevante.

## <a name="projection"></a>Projeção

Tal como os esquemas em conjuntos de dados, a projeção numa fonte define as colunas de dados, tipos e formatos a partir dos dados de origem. Para a maioria dos tipos de conjuntos de dados, como SQL e Parquet, a projeção numa fonte é fixada para refletir o esquema definido num conjunto de dados. Quando os seus ficheiros de origem não são fortemente digitados (por exemplo, ficheiros planos .csv em vez de ficheiros Parquet), pode definir os tipos de dados para cada campo na transformação da fonte.

![Screenshot que mostra definições no separador Projeção.](media/data-flow/source3.png "Screenshot que mostra definições no separador Projeção.")

Se o seu ficheiro de texto não tiver um esquema definido, selecione **Detetar o tipo de dados** para que a Data Factory recolha e infera os tipos de dados. Selecione **Definir o formato predefinido** para fazer adetectação automática dos formatos de dados predefinidos.

**O esquema de reset** repõe a projeção para o que é definido no conjunto de dados referenciado.

Pode modificar os tipos de dados da coluna numa transformação de colunas derivadas a jusante. Utilize uma transformação selecionada para modificar os nomes das colunas.

### <a name="import-schema"></a>Esquema de importação

Selecione o botão **de esquema de importação** no **separador Projeção** para utilizar um cluster de depuração ativo para criar uma projeção de esquema. Está disponível em todos os tipos de origem. Importar o esquema aqui irá sobrepor-se à projeção definida no conjunto de dados. O objeto do conjunto de dados não será alterado.

Importar esquemas é útil em conjuntos de dados como Avro e Azure Cosmos DB que suportam estruturas de dados complexas que não requerem definições de esquemas no conjunto de dados. Para conjuntos de dados inline, o esquema de importação é a única forma de referência de metadados de coluna sem deriva de esquema.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação da fonte

O **separador Otimize** permite a edição de informações de partição em cada passo de transformação. Na maioria dos casos, a partilha atual de utilização irá **otimizar** para a estrutura de partição ideal para uma fonte.

Se estiver a ler a partir de uma fonte de base de dados Azure SQL, a partição de **fonte** personalizada provavelmente lerá os dados mais rápido. A Data Factory irá ler grandes consultas fazendo ligações à sua base de dados em paralelo. Esta partição de fonte pode ser feita numa coluna ou através de uma consulta.

![Screenshot que mostra as definições de partição Source.](media/data-flow/sourcepart3.png "Screenshot que mostra as definições de partição Source.")

Para obter mais informações sobre a otimização dentro do fluxo de dados de mapeamento, consulte o [separador Otimize](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passos seguintes

Comece a construir o seu fluxo de dados com uma [transformação de colunas derivadas](data-flow-derived-column.md) e uma [transformação selecionada.](data-flow-select.md)
