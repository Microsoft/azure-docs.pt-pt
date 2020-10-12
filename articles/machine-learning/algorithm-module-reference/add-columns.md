---
title: 'Adicionar Colunas: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Add Columns em Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 17882581c4548996c9a8b2a8670b5f720e64da1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899004"
---
# <a name="add-columns-module"></a>Adicionar módulo de colunas

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para concatenar dois conjuntos de dados. Combina todas as colunas dos dois conjuntos de dados que especifica como entradas para criar um único conjunto de dados. Se precisar de concatenar mais de dois conjuntos de dados, utilize várias instâncias de **Add Columns**.



## <a name="how-to-configure-add-columns"></a>Como configurar adicionar colunas
1. Adicione o módulo **Add Columns** ao seu oleoduto.

2. Ligue os dois conjuntos de dados que pretende concatenar. Se quiser combinar mais de dois conjuntos de dados, pode acorrentar várias combinações de **Add Columns**.

    - É possível combinar duas colunas que têm um número diferente de linhas. O conjunto de dados de saída é acolchoado com valores em falta para cada linha na coluna de origem mais pequena.

    - Não é possível escolher colunas individuais para adicionar. Todas as colunas de cada conjunto de dados são concatenadas quando utiliza **Colunas adicionais**. Portanto, se pretender adicionar apenas um subconjunto das colunas, utilize Colunas Selecionadas no Conjunto de Dados para criar um conjunto de dados com as colunas desejadas.

3. Envie o oleoduto.

### <a name="results"></a>Results
Depois de ter funcionado o gasoduto:

- Para ver as primeiras linhas do novo conjunto de dados, clique com o botão direito no módulo **Add Columns** e selecione Visualize. Ou selecione o módulo e mude para o **separador Saídas** no painel direito, clique no ícone histograma nas **saídas** da Porta para visualizar o resultado.

O número de colunas no novo conjunto de dados é igual à soma das colunas de ambos os conjuntos de dados de entrada.

Se existirem duas colunas com o mesmo nome nos conjuntos de dados de entrada, é adicionado um sufixo numérico ao nome da coluna. Por exemplo, se houver dois casos de uma coluna chamada TargetOutcome, a coluna esquerda seria renomeada TargetOutcome_1 e a coluna direita seria renomeada TargetOutcome_2.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 