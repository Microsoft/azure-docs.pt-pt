---
title: Criar fábrica de dados do Azure, mapeamento de fluxo de dados
description: Criar fábrica de dados do Azure, mapeamento de fluxo de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bb6ae9f97d681625218118b8adca116de1c0fb21
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728238"
---
# <a name="create-azure-data-factory-data-flow"></a>Criar fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Fluxos de dados de mapeamento no ADF proporcionam uma forma para transformar dados em escala sem necessidade de qualquer codificação. Pode criar uma tarefa de transformação de dados no estruturador de fluxo de dados com a construção de uma série de transformações. Comece com qualquer número de transformações de origem seguido os passos de transformação de dados. Em seguida, conclua o fluxo de dados com o sink para firmar os resultados num destino.

Comece por criar primeiro uma nova fábrica de dados V2 a partir do Portal do Azure. Depois de criar a fábrica de novo, clique no mosaico "Criar e monitorizar" para iniciar a IU do Data Factory.

![Opções de fluxo de dados](media/data-flow/v2dataflowportal.png "criar fluxo de dados")

Assim que estiver na IU do Data Factory, pode utilizar fluxos de dados de exemplo. Os exemplos estão disponíveis a partir da Galeria de modelo do ADF. No ADF, crie "Pipeline de modelo" e selecione a categoria de fluxo de dados a partir da galeria do modelo.

![Opções de fluxo de dados](media/data-flow/template.png "criar fluxo de dados")

Será solicitado para introduzir as suas informações de conta de armazenamento de Blobs do Azure.

![Opções de fluxo de dados](media/data-flow/template2.png "fluxo de dados criar 2")

[Os dados usados para estes exemplos podem ser encontrados aqui](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Transferir os dados de exemplo e armazenar os ficheiros nas suas contas de armazenamento de Blobs do Azure, para que pode executar os exemplos.

## <a name="create-new-data-flow"></a>Criar novo fluxo de dados

O recurso de criar "sinal de adição" botão na IU do ADF para criar fluxos de dados de utilização

![Opções de fluxo de dados](media/data-flow/newresource.png "novo recurso")

## <a name="next-steps"></a>Passos Seguintes

Comece a criar a sua transformação de dados com um [transformação de origem](data-flow-source.md).
