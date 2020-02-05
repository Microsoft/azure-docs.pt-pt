---
title: Transformação de origem no fluxo de dados de mapeamento
description: Saiba como configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 128b15bd5b3ba3c3ac891719bf5c3ec8e5137cce
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023519"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformação de origem no fluxo de dados de mapeamento 

Uma transformação de origem configura sua fonte de dados para o fluxo de dados. Ao criar fluxos de dados, sua primeira etapa sempre estará configurando uma transformação de origem. Para adicionar uma origem, clique na caixa **Adicionar origem** na tela fluxo de dados.

Cada fluxo de dados requer pelo menos uma transformação de origem, mas você pode adicionar quantas fontes forem necessárias para concluir as transformações de dados. Você pode unir essas fontes junto com uma transformação junção, pesquisa ou União.

Cada transformação de origem é associada a exatamente um conjunto de Data Factory. O DataSet define a forma e o local dos dados que você deseja gravar ou ler. Se estiver usando um conjunto de informações baseado em arquivo, você poderá usar curingas e listas de arquivos em sua fonte para trabalhar com mais de um arquivo por vez.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Conectores de origem com suporte no fluxo de dados de mapeamento

O mapeamento de fluxo de dados segue uma abordagem de extração, carregamento, transformação (ELT) e funciona com conjuntos de dados de *preparo* que estão todos no Azure. Atualmente, os seguintes conjuntos de valores podem ser usados em uma transformação de origem:
    
* [Armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, texto, parquet)
* [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, texto, parquet)
* [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, texto, parquet)
* [Análise de Synapse do Azure](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [CosmosDB do Azure](connector-azure-cosmos-db.md#mapping-data-flow-properties)

As configurações específicas para esses conectores estão localizadas na guia **Opções de origem** . as informações sobre essas configurações estão localizadas na documentação do conector. 

Azure Data Factory tem acesso a mais de [90 conectores nativos](connector-overview.md). Para incluir dados dessas outras fontes em seu fluxo de dados, use a atividade de cópia para carregar esses dados em uma das áreas de preparo com suporte.

## <a name="source-settings"></a>Definições de origem

Depois de adicionar uma fonte, configure por meio da guia **configurações de origem** . Aqui, você pode escolher ou criar o conjunto de os pontos de origem em. Você também pode selecionar opções de esquema e amostragem para seus dados.

![Guia Configurações de origem](media/data-flow/source1.png "Guia Configurações de origem")

**Ligação de teste:** Teste se o serviço de faíscas do fluxo de dados pode ou não ligar-se com sucesso ao serviço ligado utilizado no seu conjunto de dados de origem. O modo dedepuração deve estar ligado para que esta funcionalidade esteja ativada.

**Descompasso de esquema:** a [descompasso de esquema](concepts-data-flow-schema-drift.md) é a capacidade do data Factory de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem a necessidade de definir explicitamente as alterações na coluna.

* Marque a caixa **permitir descompasso de esquema** se as colunas de origem forem alteradas com frequência. Essa configuração permite que todos os campos de origem de entrada fluam por meio das transformações para o coletor.

* Escolher **inferir tipos de coluna descompassos** instruirá data Factory a detectar e definir tipos de dados para cada nova coluna descoberta. Com esse recurso desativado, todas as colunas descompassos serão do tipo cadeia de caracteres.

**Validar esquema:** Se a validação de esquema for selecionada, o fluxo de dados não será executado se os dados de origem de entrada não corresponderem ao esquema definido do DataSet.

**Ignorar contagem de linhas:** O campo ignorar contagem de linhas especifica quantas linhas ignorar no início do conjunto de um.

**Amostragem:** Habilite a amostragem para limitar o número de linhas de sua origem. Use essa configuração quando você testar ou criar amostras de dados de sua fonte para fins de depuração.

**Linhas multilinha:** Selecione linhas multilinha se o arquivo de texto de origem contiver valores de cadeia de caracteres que abranjam várias linhas, ou seja, linhas novas dentro de um valor. Essa configuração só está disponível em conjuntos de DelimitedText.

Para validar se a fonte está configurada corretamente, ative o modo de depuração e busque uma visualização de dados. Para obter mais informações, consulte [modo de depuração](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando o modo de depuração estiver ativado, a configuração de limite de linha nas configurações de depuração substituirá a configuração de amostragem na origem durante a visualização de dados.

## <a name="projection"></a>Projeção

Como os esquemas em conjuntos de dados, a projeção em uma fonte define as colunas, os tipos e os formatos dos dados de origem. Para a maioria dos tipos de conjunto de conjuntos, como SQL e parquet, a projeção em uma fonte é fixada para refletir o esquema definido em um conjunto de um DataSet. Quando os arquivos de origem não são fortemente tipados (por exemplo, arquivos CSV simples em vez de arquivos parquet), você pode definir os tipos de dados para cada campo na transformação de origem.

![Configurações na guia projeção](media/data-flow/source3.png "Projeção")

Se o arquivo de texto não tiver um esquema definido, selecione **detectar tipo de dados** para que data Factory obterá amostras e inferirá os tipos de dados. Selecione **definir formato padrão** para detectar automaticamente os formatos de dados padrão.

**Redefinir** o esquema repõe a projeção para o que é definido no conjunto de dados referenciado.

Você pode modificar os tipos de dados de coluna em uma transformação de coluna derivada de fluxo inferior. Use uma transformação selecionar para modificar os nomes de coluna.

### <a name="import-schema"></a>Importar esquema

O botão **Import Schema** no separador **Projeção** permite-lhe utilizar um cluster de depuração ativo para criar uma projeção de esquemas. Disponível em todos os tipos de origem, importar o esquema aqui substituirá a projeção definida no conjunto de dados. O objeto conjunto de dados não será alterado.

Isto é útil em conjuntos de dados como a Avro e a CosmosDB que suportam estruturas de dados complexas não requerem definições de esquemas para existir no conjunto de dados.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação de origem

Na guia **otimizar** da transformação origem, você poderá ver um tipo de partição de **origem** . Essa opção só está disponível quando sua origem é o banco de dados SQL do Azure. Isso ocorre porque Data Factory tenta fazer conexões paralelas para executar consultas grandes em sua origem do banco de dados SQL.

![Configurações de partição de origem](media/data-flow/sourcepart3.png "particionamento")

Você não precisa Particionar dados em sua origem do banco do dados SQL, mas as partições são úteis para consultas grandes. Você pode basear sua partição em uma coluna ou em uma consulta.

### <a name="use-a-column-to-partition-data"></a>Usar uma coluna para particionar dados

Na tabela de origem, selecione uma coluna na qual particionar. Defina também o número de partições.

### <a name="use-a-query-to-partition-data"></a>Usar uma consulta para particionar dados

Você pode optar por particionar as conexões com base em uma consulta. Insira o conteúdo de um predicado WHERE. Por exemplo, digite year > 1980.

Para obter mais informações sobre a otimização no fluxo de dados de mapeamento, consulte a [guia otimizar](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passos seguintes

Comece a criar uma [transformação de coluna derivada](data-flow-derived-column.md) e uma [transformação selecionar](data-flow-select.md).
