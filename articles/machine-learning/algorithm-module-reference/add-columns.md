---
title: 'Adicione colunas: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de adicionar colunas no serviço Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029359"
---
# <a name="add-columns-module"></a>Adicionar o módulo de colunas

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para concatenar dois conjuntos de dados. Combinar todas as colunas de dois conjuntos de dados que especificar como entradas para criar um único conjunto de dados. Se precisar de mais de dois conjuntos de dados de concatenar, utilizar várias instâncias do **adicionar colunas**.



## <a name="how-to-configure-add-columns"></a>Como configurar adicionar colunas
1. Adicionar a **adicionar colunas** módulo à sua experimentação.

2. Ligue-se os dois conjuntos de dados que pretende concatenar. Se pretender combinar mais de dois conjuntos de dados, pode encadear juntos várias combinações de **adicionar colunas**.

    - É possível combinar duas colunas que têm um número diferente de linhas. O conjunto de dados de saída é preenchido com valores em falta para cada linha na coluna de origem mais pequena.

    - Não é possível escolher colunas individuais para adicionar. Todas as colunas de cada conjunto de dados são concatenadas quando usa **adicionar colunas**. Portanto, se desejar adicionar apenas um subconjunto das colunas, use selecionar colunas no conjunto de dados para criar um conjunto de dados com as colunas que pretende.

3. Execute a experimentação.

### <a name="results"></a>Resultados
Após a execução a experimentação:

- Para ver as primeiras linhas do conjunto de dados nova, clique com botão direito a saída de **adicionar colunas** e selecione o visualizar.

O número de colunas no conjunto de dados nova é igual a soma das colunas de ambos os conjuntos de dados de entrada.

Se existirem duas colunas com o mesmo nome nos conjuntos de dados de entrada, é adicionado um sufixo numérico ao nome da coluna. Por exemplo, se existirem duas instâncias de uma coluna com o nome TargetOutcome, a coluna da esquerda seria possível mudar o nome TargetOutcome_1 e a coluna da direita seria renomeado TargetOutcome_2.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 