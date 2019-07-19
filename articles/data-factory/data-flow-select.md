---
title: Fluxo de dados de mapeamento de Azure Data Factory selecionar transformação
description: Fluxo de dados de mapeamento de Azure Data Factory selecionar transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314191"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Fluxo de dados de mapeamento de Azure Data Factory selecionar transformação
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use essa transformação para a seletividade da coluna (reduzindo o número de colunas), colunas de alias e nomes de fluxo e reordenar colunas.

## <a name="how-to-use-select-transformation"></a>Como usar a transformação selecionar
A transformação selecionar permite que você faça o alias de um fluxo inteiro ou colunas nesse fluxo, atribua nomes diferentes (aliases) e, em seguida, referencie esses novos nomes posteriormente no fluxo de dados. Essa transformação é útil para cenários de auto-associação. A maneira de implementar uma autojunção no fluxo de dados do ADF é pegar um fluxo, ramificar a ti com "nova ramificação" e, imediatamente depois, adicionar uma transformação "Select". Esse fluxo agora terá um novo nome que você pode usar para ingressar novamente no fluxo original, criando uma auto-associação:

![] Auto-associação (media/data-flow/selfjoin.png "") Auto-associação

No diagrama acima, a transformação selecionar está na parte superior. Este é um alias do fluxo original para "OrigSourceBatting". Na transformação junção realçada abaixo dela, você pode ver que usamos essa transmissão de alias Select como a junção à direita, permitindo que possamos fazer referência à mesma chave tanto no lado esquerdo & direito da junção interna.

Select também pode ser usado como uma forma de selecionar colunas de seu fluxo de dados. Por exemplo, se você tiver 6 colunas definidas em seu coletor, mas quiser apenas escolher um 3 específico para transformar e, em seguida, fluir para o coletor, poderá selecionar apenas os 3 usando a transformação selecionar.

> [!NOTE]
> Você deve desativar "selecionar tudo" para escolher apenas colunas específicas

![Selecionar transformação](media/data-flow/select001.png "Selecionar alias")

## <a name="options"></a>Opções
* A configuração padrão para "selecionar" é incluir todas as colunas de entrada e manter esses nomes originais. Você pode definir o alias do fluxo definindo o nome da transformação selecionar.
* Para alias de colunas individuais, desmarque "selecionar tudo" e use o mapeamento de coluna na parte inferior.
* Escolha ignorar duplicatas para eliminar colunas duplicadas dos metadados de entrada ou saída.

![Ignorar duplicatas](media/data-flow/select-skip-dup.png "Ignorar duplicatas")

## <a name="next-steps"></a>Passos seguintes
* Depois de usar Select para renomear, reordenar e alias de colunas, use a [transformação do coletor](data-flow-sink.md) para colocar seus dados em um repositório de dados.
