---
title: Transformação do coletor no fluxo de dados de mapeamento
description: Saiba como configurar uma transformação de coletor no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 3b631c068d1a444691345e054219208c4c8b0b8c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020051"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformação do coletor no fluxo de dados de mapeamento

Depois de transformar seus dados, você pode coletar os dados em um conjunto de dado de destino. Cada fluxo de dados requer pelo menos uma transformação de coletor, mas você pode gravar tantos coletores quantos forem necessários para concluir o fluxo de transformação. Para gravar em coletores adicionais, crie novos fluxos por meio de novas ramificações e divisões condicionais.

Cada transformação de coletor é associada a exatamente um conjunto de Data Factory. O DataSet define a forma e o local dos dados que você deseja gravar.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Conectores de coletor com suporte no fluxo de dados de mapeamento

Atualmente, os seguintes conjuntos de valores podem ser usados em uma transformação de coletor:
    
* [Armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, texto, parquet)
* [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, texto, parquet)
* [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, texto, parquet)
* [Análise de Synapse do Azure](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Base de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [CosmosDB do Azure](connector-azure-cosmos-db.md#mapping-data-flow-properties)

As configurações específicas para esses conectores estão localizadas na guia **configurações** . as informações sobre essas configurações estão localizadas na documentação do conector. 

Azure Data Factory tem acesso a mais de [90 conectores nativos](connector-overview.md). Para gravar dados para essas outras fontes do fluxo de dados, use a atividade de cópia para carregar esses dados de uma das áreas de preparo com suporte após a conclusão do fluxo de dados.

## <a name="sink-settings"></a>Configurações do coletor

Depois de adicionar um coletor, configure por meio da guia **coletor** . Aqui, você pode escolher ou criar o conjunto de os que seu coletor grava 

![Configurações do coletor](media/data-flow/sink-settings.png "Configurações do coletor")

**Descompasso de esquema:** a [descompasso de esquema](concepts-data-flow-schema-drift.md) é a capacidade do data Factory de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem a necessidade de definir explicitamente as alterações na coluna. Habilite **permitir descompasso de esquemas** para gravar colunas adicionais sobre o que está definido no esquema de dados do coletor.

**Validar esquema:** Se validar esquema for selecionado, o fluxo de dados falhará se nenhuma coluna no esquema definido do conjunto de dados for encontrada.

## <a name="field-mapping"></a>Mapeamento de campo

Semelhante a uma transformação selecionar, na guia **mapeamento** do coletor, você pode decidir quais colunas de entrada serão gravadas. Por padrão, todas as colunas de entrada, incluindo colunas descompassos, são mapeadas. Isso é conhecido como **mapeamento automático**.

Ao desativar o mapeamento automático, você terá a opção de adicionar mapeamentos fixos baseados em colunas ou mapeamentos baseados em regras. Os mapeamentos baseados em regras permitem escrever expressões com correspondência de padrões, enquanto o mapeamento fixo mapeará nomes de colunas lógicas e físicas. Para obter mais informações sobre mapeamento baseado em regras, consulte [padrões de coluna no fluxo de dados de mapeamento](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Encomenda personalizada de pia

Por padrão, os dados são escritos a vários lavatórios por ordem não determinística. O motor de execução escreverá dados em paralelo à medida que a lógica de transformação estiver concluída e a ordem do lavatório pode variar cada execução. Para especificar e ordenar apiato exato, ative a encomenda de **pia personalizada** no separador geral do fluxo de dados. Quando ativados, os lavatórios serão escritos sequencialmente em ordem crescente.

![Encomenda personalizada de pia](media/data-flow/custom-sink-ordering.png "Encomenda personalizada de pia")

## <a name="data-preview-in-sink"></a>Visualização de dados no coletor

Ao buscar uma visualização de dados em um cluster de depuração, nenhum dado será gravado no coletor. Um instantâneo da aparência dos dados será retornado, mas nada será gravado no destino. Para testar a gravação de dados em seu coletor, execute uma depuração de pipeline na tela do pipeline.

## <a name="next-steps"></a>Passos seguintes
Agora que você criou o fluxo de dados, adicione uma [atividade de fluxo de dados ao seu pipeline](concepts-data-flow-overview.md).
