---
title: 'Adicionar colunas: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo adicionar colunas no Azure Machine Learning para concatenar dois conjuntos de valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 55fe3a2a4fd0f45b0873e47f29b7ea013558be40
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152487"
---
# <a name="add-columns-module"></a>Adicionar módulo de colunas

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para concatenar dois conjuntos de valores. Você combina todas as colunas dos dois conjuntos de valores que você especifica como entradas para criar um único conjunto de informações. Se precisar de concatenar mais de dois conjuntos de dados, utilize várias instâncias de **Adicionar Colunas**.



## <a name="how-to-configure-add-columns"></a>Como configurar adicionar colunas
1. Adicione o módulo **Adicionar Colunas** ao seu pipeline.

2. Conecte os dois conjuntos de os que você deseja concatenar. Se quiser combinar mais de dois conjuntos de dados, pode acorrentar várias combinações de **Colunas Add**.

    - É possível combinar duas colunas que têm um número diferente de linhas. O conjunto de resultados de saída é preenchido com valores ausentes para cada linha na coluna de origem menor.

    - Você não pode escolher colunas individuais para adicionar. Todas as colunas de cada conjunto de dados são concatenadas quando utiliza **Colunas Add**. Portanto, se você quiser adicionar apenas um subconjunto das colunas, use selecionar colunas no conjunto de conjuntos para criar um conjunto de um DataSet com as colunas desejadas.

3. Executar o pipeline.

### <a name="results"></a>Resultados
Após a execução do pipeline:

- Para ver as primeiras linhas do novo conjunto de dados, clique no módulo **Adicionar Colunas** e selecione Visualize. Ou Selecione o módulo e mude para o separador **Saídas** no painel direito, clique no ícone histograma nas **saídas da Porta** para visualizar o resultado.

O número de colunas no novo DataSet é igual à soma das colunas de ambos os conjuntos de dados de entrada.

Se houver duas colunas com o mesmo nome nos conjuntos de dados de entrada, um sufixo numérico será adicionado ao nome da coluna. Por exemplo, se houver dois casos de uma coluna chamada TargetOutcome, a coluna esquerda seria renomeada TargetOutcome_1 e a coluna direita seria renomeada TargetOutcome_2.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 