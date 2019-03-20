---
title: Imagens moderadas com a consola de API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilize a API de moderação de imagens do Azure Content Moderator para iniciar fluxos de trabalho de análise de revisão e moderação de conteúdo da imagem.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 1e4efa5e06525194bfdc7d1932fcfec5ec9f8c6b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093825"
---
# <a name="moderate-images-from-the-api-console"></a>Moderado imagens a partir da consola de API

Utilize o [API de moderação de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) no Azure Content Moderator para iniciar fluxos de trabalho de análise de revisão e moderação de conteúdo da imagem. A tarefa de moderação analisa os seus conteúdos relativamente a profanidades e compara-os aos listas de bloqueios personalizadas e partilhadas.

## <a name="use-the-api-console"></a>Utilizar a consola de API
Antes de pode testar a API na consola do online, terá a chave de subscrição. Isto está localizado no **configurações** separador a **Ocp-Apim-Subscription-Key** caixa. Para obter mais informações, veja [Descrição geral](overview.md).

1. Aceda a [referência da API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   O **imagem - avaliar** é aberta a página de moderação de imagens.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Experimentar a imagem – avaliar a seleção de região de página](images/test-drive-region.png)
  
   O **imagem - avaliar** é aberta a consola de API.

3. Na **Ocp-Apim-Subscription-Key** , introduza a chave de subscrição.

   ![Experimentar a imagem – avaliar a chave de subscrição de consola](images/try-image-api-1.PNG)

4. Na **corpo do pedido** caixa, utilizar a imagem de exemplo predefinida ou especificar uma imagem a analisar. Pode enviar a imagem propriamente dita como binário bit de dados, ou especificar um URL acessível publicamente para uma imagem. 

   Neste exemplo, utilize o caminho indicado no **corpo do pedido** caixa e, em seguida, selecione **enviar**. 

   ![Experimentar a imagem – avalie o corpo do pedido de consola](images/try-image-api-2.PNG)

   Essa é a imagem neste URL:

   ![Experimentar a imagem – avaliar a imagem de exemplo do console](images/sample-image.jpg) 

5. Selecione **Enviar**.

6. A API devolve uma pontuação de probabilidade para cada classificação. Ele também retorna a determinação de se a imagem satisfaz as condições (**true** ou **falso**). 

   ![Tente imagem - avaliar a pontuação de probabilidade de consola e a determinação de condição](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Deteção de rostos

Pode utilizar a API de moderação de imagens para localizar rostos numa imagem. Esta opção pode ser útil quando tem problemas de privacidade e pretende impedir que um rosto específico que está a ser publicado na sua plataforma. 

1. Na [referência de API de moderação de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), no menu à esquerda, sob **imagem**, selecione **encontrar enfrenta**. 

   O **imagem - encontrar enfrenta** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Experimentar a imagem – encontrar a seleção de região de página de rostos](images/test-drive-region.png)

   O **imagem - encontrar enfrenta** é aberta a consola de API.

3. Especifique uma imagem para analisar. Pode enviar a imagem propriamente dita como binário bit de dados ou especifique um URL acessível publicamente para uma imagem. Este links de exemplo para uma imagem que é utilizada numa história da CNN mais populares.

   ![Experimentar a imagem – encontrou a imagem de exemplo de rostos](images/try-image-api-face-image.jpg)

   ![Experimentar a imagem – encontrar rostos de pedido de exemplo](images/try-image-api-face-request.png)

4. Selecione **Enviar**. Neste exemplo, a API localiza os dois rostos e devolve suas coordenadas na imagem.

   ![Experimentar a imagem – localize a caixa de conteúdo de resposta de exemplo de rostos](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Deteção de texto através da capacidade de OCR

Pode utilizar a capacidade de OCR de moderador conteúdos para detetar texto em imagens.

1. Na [referência de API de moderação de imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), no menu à esquerda, sob **imagem**, selecione **OCR**. 

   O **imagem - OCR** é aberta a página.

2. Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

   ![Imagem - seleção de região de página de OCR](images/test-drive-region.png)

   O **imagem - OCR** é aberta a consola de API.

3. Na **Ocp-Apim-Subscription-Key** , introduza a chave de subscrição.

4. Na **corpo do pedido** caixa, utilize a imagem predefinida do exemplo. Esta é a mesma imagem que é utilizada na secção anterior.

5. Selecione **Enviar**. O texto extraído é apresentado no JSON:

   ![Imagem - caixa de conteúdo de resposta de exemplo de OCR](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Passos Seguintes

Utilizar a API REST no seu código ou começar com o [guia de introdução de .NET de moderação de imagem](image-moderation-quickstart-dotnet.md) para integrar com a sua aplicação.
