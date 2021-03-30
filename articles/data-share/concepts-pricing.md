---
title: Compreender os preços da partilha de dados do Azure
description: Saiba como funciona o preço da Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88137043"
---
# <a name="understand-azure-data-share-pricing"></a>Compreender os preços da partilha de dados do Azure

Para partilha baseada em instantâneos, os encargos de partilha de dados do Azure para dataset Snapshot e execução instantânea. Este artigo explica como estimar dataset Snapshot e Execução instantânea usando informações de histórico instantâneo. Atualmente, o fornecedor de dados é faturado para o Dataset Snapshot e a Execução Instantânea.

## <a name="dataset-snapshot"></a>Instantâneo do conjunto de dados

Dataset Snapshot é a operação para mover o conjunto de dados da sua origem para o destino. Quando o instantâneo é tomado para uma partilha, a imagem de cada conjunto de dados dentro da partilha é uma operação Dataset Snapshot. Siga os passos abaixo para ver uma lista de instantâneos do conjunto de dados. 

1. No portal Azure, navegue para o seu recurso Data Share.

1. Selecione uma ação de Partilha enviada ou Partilha Recebida.

1. Clique no **separador Histórico.**

1. Clique na hora de início de uma foto.
 
    ![História do instantâneo](./media/concepts/concepts-pricing/pricing-snapshot-history.png "História do instantâneo") 

1. Veja a lista de operações do Dataset Snapshot. Cada item de linha corresponde a uma operação Dataset Snapshot. Neste exemplo, existem dois Dataset Snapshots.

    ![Operação Snapshot do conjunto de dados](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Operação Snapshot do conjunto de dados")

## <a name="snapshot-execution"></a>Execução instantânea

A Execução Instantânea inclui os recursos necessários para mover um conjunto de dados da fonte para o destino. Para cada operação de instantâneo do conjunto de dados, a Execução instantânea é calculada como número de vCores multiplicados pela duração do instantâneo. As acusações são prostimadas a cada minuto e arredondadas. O número de vCore é selecionado dinamicamente com base no par de alvos de origem e no padrão de dados. Siga os passos abaixo para ver a hora de início do instantâneo, o tempo de fim e vCores utilizados para uma operação de instantâneo do conjunto de dados.

1. No portal Azure, navegue para o seu recurso Data Share.

1. Selecione uma ação de Partilha enviada ou Partilha Recebida.

1. Clique no **separador Histórico.**

1. Clique na hora de início de uma foto.

    ![História do instantâneo](./media/concepts/concepts-pricing/pricing-snapshot-history.png "História do instantâneo") 

1. Clique no Estado de uma operação de instantâneo do conjunto de dados.

    ![Estado do instantâneo do conjunto de dados](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Estado do instantâneo do conjunto de dados")

1. Ver a hora de início, o tempo de fim e vCores utilizados para esta operação de instantâneo do conjunto de dados. 

    ![Execução instantânea](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Execução instantânea")

## <a name="next-steps"></a>Passos seguintes

- Obtenha as últimas informações sobre preços - [Preços de Azure Data Share](https://azure.microsoft.com/pricing/details/data-share/)
- Use calculadora de preços Azure para estimar o custo - [Calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/)
