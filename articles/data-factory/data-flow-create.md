---
title: Criar um fluxo de dados de mapeamento
description: Como criar um fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 2eb455ba6fa40538bfa03018be47232066036c23
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930429"
---
# <a name="create-azure-data-factory-data-flow"></a>Criar Azure Data Factory fluxo de dados



O mapeamento de fluxos de dados no ADF fornece uma maneira de transformar dados em escala sem qualquer codificação necessária. Você pode criar um trabalho de transformação de dados no designer de fluxo de dados construindo uma série de transformações. Comece com qualquer número de transformações de origem seguidas pelas etapas de transformação de dados. Em seguida, conclua seu fluxo de dados com o coletor para colocar os resultados em um destino.

Comece pela primeira vez criando uma nova Data Factory v2 do portal do Azure. Depois de criar sua nova fábrica, clique no bloco "criar & Monitor" para iniciar a interface do usuário do Data Factory.

![Opções de fluxo de dados](media/data-flow/v2portal.png "criação de fluxo de dados")

Quando estiver na interface do usuário do Data Factory, você poderá usar fluxos de dados de exemplo. Os exemplos estão disponíveis na Galeria de modelos do ADF. No ADF, crie "pipeline do modelo" e selecione a categoria de fluxo de dados na Galeria de modelos.

![Opções de fluxo de dados](media/data-flow/template.png "criação de fluxo de dados")

Você será solicitado a inserir as informações da conta de armazenamento de BLOBs do Azure.

![Opções de fluxo de dados](media/data-flow/template2.png "criação de fluxo de dados 2")

[Os dados usados para esses exemplos podem ser encontrados aqui](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Baixe os dados de exemplo e armazene os arquivos em suas contas de armazenamento de BLOBs do Azure para que você possa executar os exemplos.

## <a name="create-new-data-flow"></a>Criar novo fluxo de dados

Use o botão criar recurso "sinal de adição" na interface do usuário do ADF para criar fluxos de dados.

![Opções de fluxo de dados](media/data-flow/newresource.png "Novo recurso")

## <a name="next-steps"></a>Passos seguintes

Comece a criar sua transformação de dados com uma [transformação de origem](data-flow-source.md).
