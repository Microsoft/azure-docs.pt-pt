---
title: Mapeamento de transformação de selecione de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de selecione de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729343"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Mapeamento de transformação de selecione de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usar essa transformação para seletividade de coluna (reduzir o número de colunas) ou para colunas de alias e nomes de fluxo.

A transformação selecione permite-lhe para alias de um fluxo de todo ou colunas desse fluxo, atribuir nomes diferentes (aliases) e, em seguida, fazer referência a esses nomes de novo mais tarde no seu fluxo de dados. Essa transformação é útil para cenários de associação automática. A forma de implementar a associação automática de mensagens em fila no fluxo de dados do ADF é dar um fluxo, ramo-lo com "Novo ramo", em seguida, imediatamente depois disso, adicione uma transformação "Select". Esse fluxo terá um novo nome, que pode utilizar para associar novamente para o fluxo original, criando uma associação automática:

![Associação automática](media/data-flow/selfjoin.png "associação automática")

No diagrama acima, a transformação selecione é na parte superior. Este é o aliasing o fluxo original a "OrigSourceBatting". A transformação de associação de higlighted abaixo dela, pode ver que utilizamos esta sequência de alias Selecione como a associação a um direita, permitindo-nos fazer referência a mesma chave no lado esquerdo & no lado direito da associação interna.

Selecione também pode ser utilizado como uma forma anule a seleção colunas a partir do seu fluxo de dados. Por exemplo, se tiver 6 colunas definidas no seu coletor, mas pretender escolher um 3 específico para transformar e, em seguida, fluir para o sink, pode selecionar apenas os 3 usando a transformação selecione.

> [!NOTE]
> Deve optar por desativar "Selecionar tudo" para selecionar apenas as colunas específicas

Opções

A definição de "Select" predefinida é incluir todas as colunas de entrada e manter esses nomes originais. Pode alias o fluxo ao definir o nome da transformação selecione.

Para colunas individuais de alias, desmarque "Selecionar tudo" e utilizar o mapeamento de colunas na parte inferior.

![Selecione a transformação](media/data-flow/select001.png "selecione Alias")
