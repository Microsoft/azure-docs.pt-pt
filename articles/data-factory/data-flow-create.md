---
title: Criar um fluxo de dados de mapeamento
description: Como criar um fluxo de dados de mapeamento da Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416517"
---
# <a name="create-azure-data-factory-data-flow"></a>Create Azure Data Factory Data Flow (Criar um Fluxo de Dados do Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mapear fluxos de dados em ADF fornece uma forma de transformar dados em escala sem qualquer codificação necessária. Você pode projetar um trabalho de transformação de dados no designer de fluxo de dados construindo uma série de transformações. Comece com qualquer número de transformações de origem seguidas por passos de transformação de dados. Em seguida, complete o seu fluxo de dados com pia para aterrar os seus resultados num destino.

Começa por criar uma nova Fábrica de Dados V2 a partir do portal Azure. Depois de criar a sua nova fábrica, clique no azulejo "Author & Monitor" para lançar a UI da Fábrica de Dados.

![Opções de Fluxo de Dados](media/data-flow/v2portal.png "fluxo de dados criar")

Uma vez que esteja na UI da Fábrica de Dados, pode utilizar fluxos de dados de amostra. As amostras estão disponíveis na Galeria de Modelos ADF. Em ADF, crie "Pipeline from Template" e selecione a categoria Data Flow a partir da galeria do modelo.

![Opções de Fluxo de Dados](media/data-flow/template.png "fluxo de dados criar")

Será solicitado que introduza a sua conta de armazenamento Azure Blob.

![Opções de Fluxo de Dados](media/data-flow/template2.png "fluxo de dados criar 2")

[Os dados utilizados para estas amostras podem ser consultados aqui.](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata) Descarregue os dados da amostra e guarde os ficheiros nas suas contas de armazenamento Azure Blob para que possa executar as amostras.

## <a name="create-new-data-flow"></a>Criar novo fluxo de dados

Utilize o botão "mais sinal" do recurso "plus sign" no UI ADF para criar fluxos de dados.

![Opções de Fluxo de Dados](media/data-flow/newresource.png "Novo Recurso")

## <a name="next-steps"></a>Passos seguintes

Comece a construir a sua transformação de dados com uma [transformação de fonte.](data-flow-source.md)
