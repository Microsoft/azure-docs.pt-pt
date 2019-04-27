---
title: Como migrar seu projeto para o 3.0 API
titlesuffix: Azure Cognitive Services
description: Saiba como migrar os projetos de visão personalizada da versão anterior da API para o 3.0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816445"
---
# <a name="migrate-to-the-30-api"></a>Migrar para o 3.0 API

Visão personalizada agora atingiu a disponibilidade geral e passou por uma atualização de API.
Esta atualização inclui alguns recursos novos e, importante, algumas alterações de última hora:

* A API de predição agora está dividida em duas consoante o tipo de projeto.
* A opção de exportação do Kit de desenvolvimento de ia de imagem digitalizada (VAIDK) requer a criação de um projeto de uma forma específica.
* Iterações de predefinição foram removidas em prol de uma publicação / anular a publicação de uma iteração com nome.

Este guia irá mostrar como atualizar seus projetos para trabalhar com a nova versão de API. Consulte a [notas de versão](release-notes.md) para obter uma lista completa das alterações.

## <a name="use-the-updated-prediction-api"></a>Utilizar a API de predição atualizada

2.x APIs utilizada a mesma chamada de predição para classificadores de imagem e projetos de detetor de objeto. Ambos os tipos de projeto foram aceitáveis para o **PredictImage** e **PredictImageUrl** chamadas. A partir do 3.0, podemos ter dividir esta API para que precisa fazer corresponder o tipo de projeto para a chamada:

* Uso **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** para obter previsões de indisponibilidade para projetos de classificação de imagem.
* Uso **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** para obter previsões de indisponibilidade para projetos de deteção de objeto.

## <a name="use-the-new-iteration-publishing-workflow"></a>Usar o novo fluxo de publicação de iteração

2.x APIs utilizada a iteração de predefinição ou um ID de iteração especificado para escolher a iteração a utilizar para predição. A partir de 3.0, já ADOTARAM o um fluxo de publicação no qual primeiro publicar uma iteração num nome especificado na API de treinamento. Em seguida, passe o nome para os métodos de predição para especificar quais as iterações para utilizar.

> [!IMPORTANT]
> O 3.0 APIs não utilize a funcionalidade de iteração do padrão. Até que substituímos as APIs mais antigas, pode continuar a utilizar o 2.x APIs para ativar/desativar uma iteração como predefinição. Essas APIs serão mantidas durante um período de tempo, e pode chamar o **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** método para marcar uma iteração como predefinição.

### <a name="publish-an-iteration"></a>Publicar uma iteração

Depois de uma iteração é preparada, pode disponibilizá-la para predição utilizando o **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** método. Para publicar uma iteração, terá do ID de recurso de previsão, o que está disponível na página de definições do Web site CustomVision.

![Página Definições de visão personalizada Web site com o ID de recurso de predição descrito.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Também pode obter estas informações a partir do [Portal do Azure](https://portal.azure.com) indo para o recurso de predição de visão personalizada e selecionando **propriedades**.

Assim que sua iteração é publicada, aplicações podem utilizá-lo para predição, especificando o nome em sua chamada de API de predição. Para fazer uma iteração indisponível para chamadas de predição, use o **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="additional-export-options"></a>Opções de exportação adicionais

Com o 3.0 APIs expomos dois adicionais exportar destinos: Arquitetura ARM e o Kit de programação de ia de visão.

* Para utilizar o ARM, basta escolher um domínio Compact e, em seguida, escolha o DockerFile e, em seguida, ARM como as opções de exportação.
* Kit de desenvolvimento de ia de visão, o projeto tem de ser criado com o __gerais (Compact)__ argumento de plataformas de exportação de domínio, bem como especificar VAIDK no destino.

## <a name="next-steps"></a>Passos Seguintes

* [Documentação de referência de API de treinamento (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentação de referência da API de predição (REST)](https://go.microsoft.com/fwlink/?linkid=865445)