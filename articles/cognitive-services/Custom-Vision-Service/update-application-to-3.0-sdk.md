---
title: Como atualizar o seu projeto para o 3.0 API
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar os projetos Custom Vision da versão anterior da API para o 3.0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647504"
---
# <a name="update-to-the-30-api"></a>Atualização para o 3.0 API

A Custom Vision já chegou à Disponibilidade Geral e foi submetida a uma atualização da API.
Esta atualização inclui algumas novidades e, acima de tudo, algumas alterações de rutura:

* A API de Previsão está agora dividida em duas com base no tipo de projeto.
* A opção de exportação Vision AI Developer Kit (VAIDK) requer a criação de um projeto de forma específica.
* As iterações predefinidas foram removidas a favor de uma publicação/não publicar uma iteração nomeada.

Este guia irá mostrar-lhe como atualizar os seus projetos para trabalhar com a nova versão API. Consulte as notas de [Lançamento](release-notes.md) para obter uma lista completa das alterações.

## <a name="use-the-updated-prediction-api"></a>Utilize a API de Previsão atualizada

As APIs 2.x usaram a mesma chamada de previsão para classificadores de imagem e projetos de detetor de objetos. Ambos os tipos de projeto saem aceitáveis para as chamadas **PredictImage** e **PredictImageUrl.** A partir do 3.0, dividimos esta API para que tenha de combinar o tipo de projeto com a chamada:

* Utilize **[a ClassImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[a ClassImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** para obter previsões para projetos de classificação de imagem.
* Utilize **[detectimage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[detectimageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** para obter previsões para projetos de deteção de objetos.

## <a name="use-the-new-iteration-publishing-workflow"></a>Use o novo fluxo de trabalho de publicação de iteração

As 2.x APIs usaram a iteração padrão ou um ID de iteração especificado para escolher a iteração para usar para previsão. A partir de 3.0, adotámos um fluxo de publicação pelo qual publica pela primeira vez uma iteração com um nome especificado da API de formação. Em seguida, passa o nome para os métodos de previsão para especificar qual a iteração a usar.

> [!IMPORTANT]
> As 3.0 APIs não utilizam a função de iteração predefinida. Até depreitá-los mais antigos, pode continuar a usar as APIs 2.x para alternar uma iteração como padrão. Estas APIs serão mantidas por um período de tempo, e pode chamar o método **[UpdateIterato](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** para marcar uma iteração como padrão.

### <a name="publish-an-iteration"></a>Publicar uma iteração

Uma vez treinada uma iteração, pode disponibilizá-la para previsão utilizando o método **[PublishItera.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** Para publicar uma iteração, necessitará do ID de recurso de previsão, que está disponível na página de definições do site CustomVision.

![A página de definições do site Custom Vision com o ID de recurso de previsão delineado.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Também pode obter esta informação do [Portal Azure,](https://portal.azure.com) indo ao recurso de Previsão de Visão Personalizada e selecionando **Propriedades**.

Uma vez publicada a sua iteração, as aplicações podem usá-lo para previsão, especificando o nome na sua chamada Deprevisão API. Para tornar uma iteração indisponível para chamadas de previsão, utilize a API não **[publicar a Iteração.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>Passos seguintes

* [Documentação de referência da API de formação (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentação de referência da API de previsão (REST)](https://go.microsoft.com/fwlink/?linkid=865445)