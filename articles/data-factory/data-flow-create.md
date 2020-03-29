---
title: Criar um fluxo de dados de mapeamento
description: Como criar um fluxo de dados de mapeamento da Fábrica de Dados Azure
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 2eb455ba6fa40538bfa03018be47232066036c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930429"
---
# <a name="create-azure-data-factory-data-flow"></a>Create Azure Data Factory Data Flow (Criar um Fluxo de Dados do Azure Data Factory)



Mapeamento de Fluxos de Dados em ADF fornecem uma forma de transformar dados em escala sem qualquer codificação necessária. Você pode projetar um trabalho de transformação de dados no designer de fluxo de dados construindo uma série de transformações. Comece com qualquer número de transformações de origem seguidas por passos de transformação de dados. Em seguida, complete o seu fluxo de dados com pia para aterrar os seus resultados num destino.

Começa por criar primeiro uma nova V2 Data Factory a partir do portal Azure. Depois de criar a sua nova fábrica, clique no azulejo "Author & Monitor" para lançar a Data Factory UI.

![Opções de Fluxo de Dados](media/data-flow/v2portal.png "fluxo de dados criar")

Uma vez na UI da Fábrica de Dados, pode utilizar fluxos de dados de amostra. As amostras estão disponíveis na Galeria do Modelo ADF. Na ADF, crie "Pipeline from Template" e selecione a categoria Data Flow da galeria do modelo.

![Opções de Fluxo de Dados](media/data-flow/template.png "fluxo de dados criar")

Será solicitado a introduzir as informações da sua conta de Armazenamento Azure Blob.

![Opções de Fluxo de Dados](media/data-flow/template2.png "fluxo de dados criar 2")

[Os dados utilizados para estas amostras podem ser encontrados aqui.](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata) Faça o download dos dados da amostra e guarde os ficheiros nas suas contas de armazenamento Do Blob Azure para que possa executar as amostras.

## <a name="create-new-data-flow"></a>Criar novos fluxos de dados

Utilize o botão Criar recurso "sinal mais" na UI ADF para criar Fluxos de Dados.

![Opções de Fluxo de Dados](media/data-flow/newresource.png "Novo Recurso")

## <a name="next-steps"></a>Passos seguintes

Comece a construir a sua transformação de dados com uma transformação de [origem.](data-flow-source.md)
