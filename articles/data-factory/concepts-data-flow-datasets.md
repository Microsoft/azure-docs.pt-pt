---
title: Mapeamento de conjuntos de dados de fluxo de dados de fábrica de dados do Azure
description: O Azure Data Factory mapeamento de fluxo de dados tem sepecific compatibilidade de conjunto de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: c704fdba22138eb8e4610a7bc34bd4c2cc98f4ca
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432095"
---
# <a name="mapping-data-flow-datasets"></a>Conjuntos de dados de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Conjuntos de dados são uma construção de fábrica de dados que definem a forma dos dados que está a trabalhar no seu pipeline. No fluxo de dados, os dados de nível de coluna e linha requerem uma definição de conjunto de dados escalados refinado. Conjuntos de dados utilizados em pipelines de fluxo de controle não exigem a mesma profundidade de compreensão de dados.

Conjuntos de dados no fluxo de dados são utilizados nas transformações de origem e Sink. São utilizados para definir os esquemas de dados básicos. Se não tiver esquema nos seus dados, pode definir os Descompassos de esquema para a sua origem e Sink. Com o esquema definido do conjunto de dados, terá os tipos de dados relacionados, formatos de dados, localização do ficheiro e informações de ligação do serviço ligado associados. Os metadados de conjuntos de dados irão aparecer na sua transformação de origem como a origem "Projeção".

## <a name="dataset-types"></a>Tipos de conjunto de dados

Atualmente no fluxo de dados, encontrará quatro tipos de conjunto de dados:

* BD SQL do Azure
* Azure SQL DW
* Parquet (a partir do ADLS e BLOBs)
* Texto delimitado (a partir de ADLB e BLOBs)

Datasets de fluxo de dados separado a *tipo de origem* partir a *tipo de ligação de serviço ligado*. Normalmente, no Data Factory, selecione o tipo de ligação (Blob, ADLS, etc.) e, em seguida, definir o tipo de ficheiro no conjunto de dados. Dentro do fluxo de dados, escolhe os tipos de origem, o que podem ser associados com diferentes tipos de ligação de serviço ligado.

![Opções de transformação de origem](media/data-flow/dataset1.png "origens")

## <a name="data-flow-compatible-datasets"></a>Conjuntos de dados compatíveis do fluxo de dados

Ao criar um novo conjunto de dados, há uma caixa de seleção rotulada como "Dados de fluxo compatível" no canto superior direito do painel. Ao clicar nesse botão irá filtrar apenas os conjuntos de dados que podem ser utilizados com fluxos de dados. 

## <a name="import-schemas"></a>Importar esquemas

Ao importar o esquema de conjuntos de dados de fluxo de dados, verá um botão importar esquema. Ao clicar nesse botão irá apresentar-lhe duas opções: Importar da origem ou importar a partir de um ficheiro local. Na maioria dos casos, irá importar o esquema diretamente da fonte. No entanto, se tiver um ficheiro de esquema existente (ficheiro Parquet ou CSV com cabeçalhos), pode apontar para ficheiro local e o Data Factory irão definir o esquema com base nesse ficheiro de esquema.

## <a name="next-steps"></a>Passos Seguintes

Comece por [criar um novo fluxo de dados](data-flow-create.md) e adicione uma transformação de origem. Em seguida, configure o conjunto de dados para a sua origem.

