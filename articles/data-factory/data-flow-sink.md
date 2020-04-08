---
title: Transformação de afundar no fluxo de dados de mapeamento
description: Aprenda a configurar uma transformação de afundar no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 8941c097fbb2d05c3a28be87d216b7a2679ebc68
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804899"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformação de afundar no fluxo de dados de mapeamento

Depois de transformar os seus dados, pode afundar os dados num conjunto de dados de destino. Cada fluxo de dados requer pelo menos uma transformação de afundar, mas pode escrever a todos os lavatórios necessários para completar o seu fluxo de transformação. Para escrever para pias adicionais, crie novos fluxos através de novos ramos e divisões condicionais.

Cada transformação de pia está associada a exatamente um conjunto de dados da Data Factory. O conjunto de dados define a forma e localização dos dados a que pretende escrever.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Conectores de lavatórios suportados no fluxo de dados de mapeamento

Atualmente, os seguintes conjuntos de dados podem ser utilizados numa transformação de sumidouro:
    
* [Armazenamento Azure Blob](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

As definições específicas destes conectores estão localizadas no separador **Definições.** 

A Azure Data Factory tem acesso a mais de [90 conectores nativos.](connector-overview.md) Para escrever dados a essas outras fontes a partir do fluxo de dados, utilize a Atividade de Cópia para carregar esses dados a partir de uma das áreas de paragem suportadas após a conclusão do fluxo de dados.

## <a name="sink-settings"></a>Definições de sumidouro

Depois de adicionar uma pia, configure através do **separador Sink.** Aqui pode escolher ou criar o conjunto de dados para o qual a pia escreve. Abaixo está um vídeo que explica várias opções de Sink para tipos de ficheiros delimitados por texto:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Definições de sumidouro](media/data-flow/sink-settings.png "Definições de afundar")

**Deriva de schema:** [Schema Drift](concepts-data-flow-schema-drift.md) é a capacidade da fábrica de dados de lidar de forma nativa com schemas flexíveis nos fluxos de dados sem precisar de definir explicitamente as alterações de coluna. Ativar permitir que a **deriva do esquema** escreva colunas adicionais em cima do que é definido no esquema de dados do lavatório.

**Validar o esquema:** Se for selecionado o esquema de validação, o fluxo de dados falhará se alguma coluna do esquema de origem de entrada não for encontrada na projeção de origem ou se os tipos de dados não corresponderem. Utilize esta definição para fazer valer que os dados de origem satisfaça o contrato da sua projeção definida. É muito útil em cenários de origem de base de dados para sinalizar que nomes ou tipos de colunas mudaram.

## <a name="field-mapping"></a>Mapeamento de campos

Semelhante a uma transformação Select, no **separador Mapping** da pia, pode decidir quais as colunas de entrada que serão escritas. Por padrão, todas as colunas de entrada, incluindo colunas derivadas, são mapeadas. Isto é conhecido como **auto-mapeamento.**

Ao desativar o mapeamento automático, terá a opção de adicionar mapeamentos fixos baseados em colunas ou mapeamentos baseados em regras. Mapeamentos baseados em regras permitem escrever expressões com padrão correspondente, enquanto o mapeamento fixo irá mapear nomes lógicos e físicos de colunas. Para obter mais informações sobre mapeamento baseado em regras, consulte os padrões das colunas no fluxo de [dados de mapeamento](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Encomenda personalizada de pia

Por padrão, os dados são escritos a vários lavatórios por ordem não determinística. O motor de execução escreverá dados em paralelo à medida que a lógica de transformação estiver concluída e a ordem do lavatório pode variar cada execução. Para especificar e ordenar apiato exato, ative a encomenda de **pia personalizada** no separador geral do fluxo de dados. Quando ativados, os lavatórios serão escritos sequencialmente em ordem crescente.

![Encomenda personalizada de pia](media/data-flow/custom-sink-ordering.png "Encomenda personalizada de pia")

## <a name="data-preview-in-sink"></a>Pré-visualização de dados em pia

Ao obter uma pré-visualização de dados num cluster de depuração, nenhum dado será escrito na sua pia. Uma foto do que os dados parecem ser devolvidos, mas nada será escrito para o seu destino. Para testar a escrita de dados na pia, execute um depurador de gasoduto da tela do gasoduto.

## <a name="next-steps"></a>Passos seguintes
Agora que criou o seu fluxo de dados, adicione uma atividade de Fluxo de [Dados ao seu pipeline](concepts-data-flow-overview.md).
