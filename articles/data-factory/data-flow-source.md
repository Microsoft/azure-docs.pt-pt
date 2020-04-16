---
title: Transformação de origem no fluxo de dados de mapeamento
description: Aprenda a configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: e4f1369aa850a244128da470aee81b2efc2e09be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413197"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformação de origem no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Uma transformação de origem confunde a sua fonte de dados para o fluxo de dados. Ao conceber fluxos de dados, o seu primeiro passo será sempre configurar uma transformação de origem. Para adicionar uma fonte, clique na caixa **Adicionar Fonte** na tela de fluxo de dados.

Cada fluxo de dados requer pelo menos uma transformação de origem, mas pode adicionar o máximo de fontes necessárias para completar as suas transformações de dados. Pode juntar-se a essas fontes juntamente com uma adesão, procura ou transformação sindical.

Cada transformação de origem está associada a exatamente um conjunto de dados da Data Factory. O conjunto de dados define a forma e a localização dos dados a que pretende escrever ou ler. Se utilizar um conjunto de dados baseado em ficheiros, pode utilizar wildcards e listas de ficheiros na sua fonte para trabalhar com mais de um ficheiro de cada vez.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Conectores de origem suportados no fluxo de dados de mapeamento

O Mapping Data Flow segue uma abordagem de extração, carga, transformação (ELT) e trabalha com conjuntos de dados de *encenação* que estão todos em Azure. Atualmente, os seguintes conjuntos de dados podem ser utilizados numa transformação de origem:
    
* [Armazenamento Azure Blob](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

As definições específicas destes conectores estão localizadas no separador **opções Fonte.** Informações sobre estas definições estão localizadas na documentação do conector. 

A Azure Data Factory tem acesso a mais de [90 conectores nativos.](connector-overview.md) Para incluir dados dessas outras fontes no fluxo de dados, utilize a Atividade de Cópia para carregar esses dados numa das áreas de paragem suportadas.

## <a name="source-settings"></a>Definições de origem

Depois de adicionar uma fonte, configure através do separador **Definições de Origem.** Aqui pode escolher ou criar o conjunto de dados em que os seus pontos de origem se situam. Também pode selecionar opções de schema e de amostragem para os seus dados.

![Separador de definições de origem](media/data-flow/source1.png "Separador de definições de origem")

**Ligação de teste:** Teste se o serviço de faíscas do fluxo de dados pode ou não ligar-se com sucesso ao serviço ligado utilizado no seu conjunto de dados de origem. O modo dedepuração deve estar ligado para que esta funcionalidade esteja ativada.

**Deriva de schema:** [Schema Drift](concepts-data-flow-schema-drift.md) é a capacidade da fábrica de dados de lidar de forma nativa com schemas flexíveis nos fluxos de dados sem precisar de definir explicitamente as alterações de coluna.

* Verifique a caixa **de deriva de esquemas de íma** permitir se as colunas de origem mudarão frequentemente. Esta definição permite que todos os campos de origem que chegam fluam através das transformações para a pia.

* Escolher os tipos de **colunas à deriva infer** instruirá a fábrica de dados a detetar e definir tipos de dados para cada nova coluna descoberta. Com esta característica desligada, todas as colunas derivadas serão de cadeia tipo.

**Validar o esquema:** Se o esquema de validação for selecionado, o fluxo de dados não será executado se os dados de origem que chegam não corresponderem ao esquema definido do conjunto de dados.

**Contagem de linha de salto:** O campo de contagem de linha de salto especifica quantas linhas ignorar no início do conjunto de dados.

**Amostragem:** Ative a amostragem para limitar o número de linhas da sua fonte. Utilize esta definição quando testar ou provar dados da sua fonte para fins de depuração.

**Linhas multilinhas:** Selecione linhas multilinhas se o seu ficheiro de texto de origem contiver valores de cordas que abrangem várias linhas, ou seja, novas linhas dentro de um valor. Esta definição só está disponível em conjuntos de dados DelimitedText.

Para validar a sua fonte está configurada corretamente, ligue o modo de depuração e recolha uma pré-visualização de dados. Para mais informações, consulte o [modo Debug](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando o modo de depuração é ligado, a configuração do limite da linha nas definições de depuração irá sobrepor a definição de amostragem na fonte durante a pré-visualização de dados.

## <a name="projection"></a>Projeção

Tal como os esquemas em conjuntos de dados, a projeção numa fonte define as colunas de dados, tipos e formatos a partir dos dados de origem. Para a maioria dos tipos de conjuntos de dados, como o SQL e o Parquet, a projeção numa fonte é fixada para refletir o esquema definido num conjunto de dados. Quando os seus ficheiros de origem não são fortemente digitados (por exemplo, ficheiros csv planos em vez de ficheiros Parquet), pode definir os tipos de dados para cada campo na transformação de origem.

![Definições no separador Projeção](media/data-flow/source3.png "Projeção")

Se o seu ficheiro de texto não tiver esquema definido, selecione **Detetar o tipo** de dados para que a Data Factory aprove e inferirá os tipos de dados. Selecione **Definir o formato predefinido** para detetar automaticamente os formatos de dados predefinidos.

**Redefinir** o esquema repõe a projeção para o que é definido no conjunto de dados referenciado.

Pode modificar os tipos de dados da coluna numa transformação de coluna derivada a jusante. Utilize uma transformação selecionada para modificar os nomes das colunas.

### <a name="import-schema"></a>Esquema de importação

O botão **Import Schema** no separador **Projeção** permite-lhe utilizar um cluster de depuração ativo para criar uma projeção de esquemas. Disponível em todos os tipos de origem, importar o esquema aqui substituirá a projeção definida no conjunto de dados. O objeto conjunto de dados não será alterado.

Isto é útil em conjuntos de dados como a Avro e a CosmosDB que suportam estruturas de dados complexas não requerem definições de esquemas para existir no conjunto de dados.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação de fontes

No separador **Otimize** para a transformação da fonte, pode ver um tipo de partição **Fonte.** Esta opção só está disponível quando a sua fonte for base de dados Azure SQL. Isto porque a Data Factory tenta fazer ligações paralelas para executar grandes consultas contra a sua fonte de Base de Dados SQL.

![Definições de partição de origem](media/data-flow/sourcepart3.png "partição")

Não é preciso dados de partilha na sua fonte de base de dados SQL, mas as divisórias são úteis para grandes consultas. Pode basear a sua divisória numa coluna ou numa consulta.

### <a name="use-a-column-to-partition-data"></a>Utilize uma coluna para dados de partilha

A partir da sua tabela de origem, selecione uma coluna para a partilha. Também detete tese o número de divisórias.

### <a name="use-a-query-to-partition-data"></a>Use uma consulta aos dados de partilha

Pode optar por dividir as ligações com base numa consulta. Introduza o conteúdo de um predicado WHERE. Por exemplo, entrar no ano > 1980.

Para obter mais informações sobre a otimização dentro do fluxo de dados de mapeamento, consulte o [separador Otimize](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passos seguintes

Comece a construir uma [transformação de coluna derivada](data-flow-derived-column.md) e uma [transformação selecionada.](data-flow-select.md)
