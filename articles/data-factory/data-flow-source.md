---
title: Transformação de fonte no fluxo de dados de mapeamento
description: Saiba como configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 6da0c56e11b8531192ba77d8f0c27fa16eea5de2
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433306"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformação de fonte no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma transformação de fonte configura a sua fonte de dados para o fluxo de dados. Ao desenhar fluxos de dados, o seu primeiro passo será sempre configurar uma transformação de fonte. Para adicionar uma fonte, clique na caixa **Add Source** na tela de fluxo de dados.

Cada fluxo de dados requer pelo menos uma transformação de origem, mas pode adicionar o número de fontes necessárias para completar as suas transformações de dados. Pode juntar-se a essas fontes juntamente com uma junção, uma procura ou uma transformação sindical.

Cada transformação de origem está associada a exatamente um conjunto de dados da Data Factory. O conjunto de dados define a forma e a localização dos dados a que pretende escrever ou ler. Se utilizar um conjunto de dados baseado em ficheiros, pode utilizar wildcards e listas de ficheiros na sua fonte para trabalhar com mais de um ficheiro de cada vez.

## <a name="inline-datasets"></a>Conjuntos de dados inline

A primeira decisão que tomar ao criar uma transformação de fonte é se a sua informação de origem é definida dentro de um objeto de conjunto de dados ou dentro da transformação da fonte. A maioria dos formatos só estão disponíveis num ou noutro. Por favor, consulte o documento de conector apropriado para aprender a usar um conector específico.

Quando um formato é suportado tanto para inline como para um objeto conjunto de dados, existem benefícios para ambos. Os objetos do conjunto de dados são entidades reutilizáveis que podem ser alavancadas em outros fluxos de dados e atividades como copy. Estes são especialmente úteis quando se utiliza um esquema endurecido. Os conjuntos de dados não são baseados em Spark e ocasionalmente poderá ser necessário anular determinadas definições ou projeção de esquemas na transformação da fonte.

Os conjuntos de dados inline são recomendados quando se utilizam esquemas flexíveis, instâncias de origem pontuais ou fontes parametrizadas. Se a sua fonte for fortemente parametrizada, os conjuntos de dados em linha permitem-lhe não criar um objeto "manequim". Os conjuntos de dados inline são baseados em faíscas e as suas propriedades são nativas do fluxo de dados.

Para utilizar um conjunto de dados inline, selecione o formato pretendido no seletor **de tipo Fonte.** Em vez de selecionar um conjunto de dados de origem, selecione o serviço ligado ao que pretende ligar.

![Conjunto de dados inline](media/data-flow/inline-selector.png "Conjunto de dados inline")

### <a name="supported-inline-dataset-formats"></a>Formatos de conjunto de dados inline suportados

Atualmente, o único formato de conjunto de dados inline disponível é o [Modelo de Dados Comuns](format-common-data-model.md#source-properties) lido a partir da [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md).

## <a name="supported-source-datasets-in-mapping-data-flow"></a>Conjuntos de dados de origem suportados no fluxo de dados de mapeamento

Mapeamento do Fluxo de Dados segue uma abordagem de extrato, carga, transformação (ELT) e trabalha com conjuntos de dados de *encenação* que estão todos em Azure. Atualmente, os seguintes conjuntos de dados podem ser utilizados numa transformação de fonte:
    
* [Armazenamento Azure Blob](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

As definições específicas destes conectores estão localizadas no separador **opções Source.** 

O Azure Data Factory tem acesso a mais de [90 conectores nativos](connector-overview.md). Para incluir dados dessas outras fontes no seu fluxo de dados, utilize a Atividade de Cópia para carregar esses dados numa das áreas de preparação suportadas.

## <a name="source-settings"></a>Definições de origem

Depois de ter adicionado uma fonte, configufique através do separador **Definições de Origem.** Aqui pode escolher ou criar o conjunto de dados onde os seus pontos de origem. Também pode selecionar opções de esquema e amostragem para os seus dados.

![Separador de definições de origem](media/data-flow/source1.png "Separador de definições de origem")

**Ligação de teste:** Teste se o serviço de faíscas do fluxo de dados pode ou não ligar-se com sucesso ao serviço ligado utilizado no seu conjunto de dados de origem. O modo Debug deve estar ligado para que esta função seja ativada.

**Schema Drift:** [Schema Drift](concepts-data-flow-schema-drift.md) é a capacidade da fábrica de dados de lidar de forma nativa com esquemas flexíveis nos fluxos de dados sem precisar de definir explicitamente as alterações de colunas.

* Verifique a caixa **de deriva de esquema de permitir** se as colunas de origem mudarem frequentemente. Esta definição permite que todos os campos de origem que entram fluam através das transformações para a pia.

* A escolha dos tipos de **colunas derivadas infer** irá instruir a fábrica de dados a detetar e definir tipos de dados para cada nova coluna descoberta. Com esta função desligada, todas as colunas à deriva serão de tipo de cadeia.

**Validar esquema:** Se o esquema de validação for selecionado, o fluxo de dados não será executado se os dados de origem recebidas não corresponderem ao esquema definido do conjunto de dados.

**Contagem de linhas:** O campo de contagem de linhas de salto especifica quantas linhas devem ignorar no início do conjunto de dados.

**Amostragem:** Ativar a amostragem para limitar o número de linhas da sua fonte. Utilize esta definição quando testar ou recolher dados da sua fonte para fins de depuragem.

**Linhas multiline:** Selecione linhas multiline se o seu ficheiro de texto de origem contiver valores de cadeia que se estendem por várias linhas, ou seja, novas linhas dentro de um valor. Esta definição só está disponível nos conjuntos de dados DelimitedText.

Para validar a sua fonte está configurada corretamente, ligue o modo de depurar e reveja uma pré-visualização de dados. Para mais informações, consulte [o modo Debug.](concepts-data-flow-debug-mode.md)

> [!NOTE]
> Quando o modo de depurador é ligado, a configuração do limite de linha nas definições de depurador substituirá a definição de amostragem na fonte durante a pré-visualização dos dados.

## <a name="projection"></a>Projeção

Tal como os esquemas em conjuntos de dados, a projeção numa fonte define as colunas de dados, tipos e formatos a partir dos dados de origem. Para a maioria dos tipos de conjuntos de dados, como SQL e Parquet, a projeção numa fonte é fixada para refletir o esquema definido num conjunto de dados. Quando os seus ficheiros de origem não são fortemente digitados (por exemplo, ficheiros csv planos em vez de ficheiros Parquet), pode definir os tipos de dados para cada campo na transformação da fonte.

![Definições no separador Projeção](media/data-flow/source3.png "Projeção")

Se o seu ficheiro de texto não tiver um esquema definido, selecione **Detetar o tipo de dados** para que a Data Factory recolha e infera os tipos de dados. Selecione **Definir o formato predefinido** para fazer adetectação automática dos formatos de dados predefinidos.

**O esquema de reset** repõe a projeção para o que é definido no conjunto de dados referenciado.

Pode modificar os tipos de dados de coluna numa transformação de coluna derivada abaixo. Utilize uma transformação selecionada para modificar os nomes das colunas.

### <a name="import-schema"></a>Esquema de importação

O botão **Desembargador** de Importação no **separador Projeção** permite-lhe utilizar um cluster de depuração ativo para criar uma projeção de esquema. Disponível em todos os tipos de origem, a importação do esquema aqui irá sobrepor-se à projeção definida no conjunto de dados. O objeto do conjunto de dados não será alterado.

Isto é útil em conjuntos de dados como Avro e CosmosDB que suportam estruturas de dados complexas não requerem definições de esquemas no conjunto de dados.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação da fonte

No **separador Otimize** para a transformação da fonte, poderá ver um tipo de partição **Source.** Esta opção só está disponível quando a sua fonte for Azure SQL Database. Isto porque a Data Factory tenta fazer ligações paralelas para executar grandes consultas com a sua fonte de Base de Dados SQL.

![Definições de partição de origem](media/data-flow/sourcepart3.png "partição")

Não é preciso dividir dados na sua fonte de base de dados SQL, mas as divisórias são úteis para grandes consultas. Pode basear a sua partição numa coluna ou numa consulta.

### <a name="use-a-column-to-partition-data"></a>Use uma coluna para dados de partição

A partir da sua tabela de origem, selecione uma coluna para a partição. Também definir o número de divisórias.

### <a name="use-a-query-to-partition-data"></a>Use uma consulta para dados de partição

Pode optar por dividir as ligações com base numa consulta. Insira o conteúdo de um predicado WHERE. Por exemplo, entre no ano > 1980.

Para obter mais informações sobre a otimização dentro do fluxo de dados de mapeamento, consulte o [separador Otimize](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passos seguintes

Comece a construir uma [transformação de colunas derivadas](data-flow-derived-column.md) e uma [transformação selecionada.](data-flow-select.md)
