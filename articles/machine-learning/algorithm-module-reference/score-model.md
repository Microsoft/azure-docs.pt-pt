---
title: 'Modelo de pontuação: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de modelo de pontuação no serviço Azure Machine Learning para gerar predições com uma classificação de preparação ou modelo de regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029269"
---
# <a name="score-model-module"></a>Módulo do modelo de pontuação

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para gerar predições utilizando um modelo de classificação ou regressão preparado.

## <a name="how-to-use"></a>Como utilizar

1. Adicionar a **modelo de pontuação** módulo à sua experimentação.

2. Anexe um modelo preparado e um conjunto de dados que contém dados de entrada novo. 

    Os dados devem ser num formato compatível com o tipo de modelo preparado, que está a utilizar. O esquema do conjunto de dados de entrada também geralmente deve corresponder ao esquema dos dados utilizados para preparar o modelo.

3. Execute a experimentação.

## <a name="results"></a>Resultados

Depois de ter gerado um conjunto de pontuações usando [modelo de pontuação](./score-model.md):

+ Para gerar um conjunto de métricas utilizados para avaliar exatidão o modelo (desempenho).  Pode ligar-se o conjunto de dados com a pontuação [Evaluate Model](./evaluate-model.md), 
+ O módulo com o botão direito e selecione **Visualize** para ver um exemplo dos resultados.
+ Guarde os resultados para um conjunto de dados.

A pontuação ou valor previsto, pode ter muitos formatos diferentes, consoante o modelo e os dados de entrada:

- Para modelos de classificação [modelo de pontuação](./score-model.md) produz um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para modelos de regressão [modelo de pontuação](./score-model.md) gera apenas o valor numérico previsto.
- Para modelos de classificação de imagens, a pontuação poderá ser a classe do objeto na imagem ou um valor booleano que indica se um determinado recurso foi encontrado.

## <a name="publish-scores-as-a-web-service"></a>Publicar as pontuações como um serviço web

Um uso comum de classificação é retornar o resultado como parte de um serviço web preditivo. Para obter mais informações, consulte este tutorial sobre como criar um serviço da web com base numa experimentação no Azure Machine Learning:


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 