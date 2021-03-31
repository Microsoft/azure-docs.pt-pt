---
title: Como atualizar o seu projeto para a API 3.0
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar os projetos de Visão Personalizada da versão anterior da API para a API 3.0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "73647504"
---
# <a name="update-to-the-30-api"></a>Atualização para a API 3.0

A Visão Personalizada já atingiu a Disponibilidade Geral e foi submetida a uma atualização da API.
Esta atualização inclui algumas novidades e, acima de tudo, algumas alterações de rutura:

* A API de Previsão é agora dividida em duas com base no tipo de projeto.
* A opção de exportação do Kit de Desenvolvimento visionário (VAIDK) requer a criação de um projeto de uma forma específica.
* As iterações predefinidos foram removidas a favor de uma iteração com nome de publicação/não publicação.

Este guia irá mostrar-lhe como atualizar os seus projetos para trabalhar com a nova versão API. Consulte as [notas de Lançamento](release-notes.md) para obter uma lista completa das alterações.

## <a name="use-the-updated-prediction-api"></a>Utilize a API de Previsão atualizada

As APIs de 2.x usaram a mesma chamada de previsão tanto para os classificadores de imagem como para os projetos de detetores de objetos. Ambos os tipos de projeto foram aceitáveis para as chamadas **PredictImage** e **PredictImageUrl.** Começando com 3.0, dividimos esta API para que você precise combinar o tipo de projeto com a chamada:

* Use **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** para obter previsões para projetos de classificação de imagem.
* Utilize **[detectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** para obter previsões para projetos de deteção de objetos.

## <a name="use-the-new-iteration-publishing-workflow"></a>Utilize o novo fluxo de trabalho de publicação de iteração

As APIs de 2.x usaram a iteração padrão ou um ID de iteração especificado para escolher a iteração a usar para previsão. A partir de 3.0, adotamos um fluxo de publicação pelo qual publica uma iteração com um nome especificado da API de formação. Em seguida, passe o nome para os métodos de previsão para especificar qual iteração usar.

> [!IMPORTANT]
> As APIs 3.0 não utilizam a função de iteração predefinido. Até deprecarmos as APIs mais antigas, pode continuar a usar as APIs de 2,x para alternar uma iteração como padrão. Estas APIs serão mantidas por um período de tempo, e pode ligar para o método **[DeLocalização de Atualização](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** para marcar uma iteração como padrão.

### <a name="publish-an-iteration"></a>Publicar uma iteração

Uma vez que uma iteração é treinada, você pode torná-lo disponível para previsão usando o método **[De Publicação.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** Para publicar uma iteração, você precisará do ID do recurso de previsão, que está disponível na página de definições do site Da CustomVision.

![A página de configurações do site Da Visão Personalizada com o ID do recurso de previsão delineado.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Também pode obter esta informação a partir do [Portal Azure,](https://portal.azure.com) indo para o recurso de Previsão de Visão Personalizada e selecionando **Propriedades.**

Uma vez publicada a sua iteração, as aplicações podem usá-la para previsão especificando o nome na sua chamada de API de previsão. Para tornar uma iteração indisponível para chamadas de previsão, utilize a API de **[Não-Publicar.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência da API de formação (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentação de referência da API de previsão (REST)](https://go.microsoft.com/fwlink/?linkid=865445)