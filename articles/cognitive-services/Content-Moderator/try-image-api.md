---
title: Imagens moderadas com o console de API-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a API de moderação de imagem no Azure Content Moderator para iniciar fluxos de trabalho de moderação de verificação e revisão para conteúdo de imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: ce8ea76cfc1f3f30418b66cd21de43d244da6764
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043968"
---
# <a name="moderate-images-from-the-api-console"></a>Imagens moderadas do console de API

Use a [API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) no Azure Content moderator para iniciar fluxos de trabalho de moderação de verificação e revisão para conteúdo de imagem. O trabalho de moderação examina seu conteúdo em caso de profanação e o compara em listas negras personalizadas e compartilhadas.

## <a name="use-the-api-console"></a>Usar o console de API
Antes de poder testar a API no console online, você precisa da sua chave de assinatura. Isso está localizado na guia **configurações** , na caixa **OCP-APIM-Subscription-Key** . Para obter mais informações, veja [Descrição geral](overview.md).

1. Vá para [referência de API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   A página **Image-Evaluate** Image Moderation é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Testar imagem – avaliar a seleção de região de página](images/test-drive-region.png)
  
   O console de API **Image-Evaluate** é aberto.

3. Na caixa **OCP-APIM-Subscription-Key** , insira sua chave de assinatura.

   ![Experimente a imagem-avaliar a chave de assinatura do console](images/try-image-api-1.PNG)

4. Na caixa **corpo da solicitação** , use a imagem de exemplo padrão ou especifique uma imagem a ser verificada. Você pode enviar a própria imagem como dados de bit binário ou especificar uma URL acessível publicamente para uma imagem. 

   Para este exemplo, use o caminho fornecido na caixa **corpo da solicitação** e, em seguida, selecione **Enviar**. 

   ![Experimente a imagem-avaliar o corpo da solicitação do console](images/try-image-api-2.PNG)

   Esta é a imagem na URL:

   ![Testar imagem-avaliar imagem de exemplo do console](images/sample-image.jpg) 

5. Selecione **Enviar**.

6. A API retorna uma pontuação de probabilidade para cada classificação. Ele também retorna uma determinação de se a imagem atende às condições (**true** ou **false**). 

   ![Experimente a imagem-avaliar a pontuação de probabilidade do console e a determinação da condição](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Deteção de rostos

Você pode usar a API de moderação de imagem para localizar rostos em uma imagem. Essa opção pode ser útil quando você tem preocupações com privacidade e deseja impedir que uma face específica seja postada em sua plataforma. 

1. Na [referência da API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), no menu à esquerda, em **imagem**, selecione **Localizar faces**. 

   A página **imagem-localizar rostos** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Imagem de teste-seleção de região da página procurar rostos](images/test-drive-region.png)

   O console da API do **faces de localização de imagem** é aberto.

3. Especifique uma imagem a ser verificada. Você pode enviar a própria imagem como dados de bit binário ou especificar uma URL acessível publicamente para uma imagem. Este exemplo vincula a uma imagem que é usada em uma história CNN.

   ![Imagem de teste-localizar imagem – encontrar faces](images/try-image-api-face-image.jpg)

   ![Experimentar imagem – localizar exemplo de solicitação de rostos](images/try-image-api-face-request.png)

4. Selecione **Enviar**. Neste exemplo, a API localiza duas faces e retorna suas coordenadas na imagem.

   ![Imagem try-localizar imagens exemplo de conteúdo de resposta](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Detecção de texto via recurso de OCR

Você pode usar a Content Moderator capacidade de OCR para detectar texto em imagens.

1. Na [referência da API de moderação de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), no menu à esquerda, em **imagem**, selecione **OCR**. 

   A página **imagem-OCR** é aberta.

2. Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

   ![Imagem-seleção de região de página OCR](images/test-drive-region.png)

   O console de API de **imagem OCR** é aberto.

3. Na caixa **OCP-APIM-Subscription-Key** , insira sua chave de assinatura.

4. Na caixa **corpo da solicitação** , use a imagem de exemplo padrão. Essa é a mesma imagem que é usada na seção anterior.

5. Selecione **Enviar**. O texto extraído é exibido em JSON:

   ![Imagem-caixa de conteúdo de resposta de exemplo de OCR](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Passos seguintes

Use a API REST em seu código ou siga o [início rápido do SDK do .net](dotnet-sdk-quickstart.md) para adicionar moderação de imagem ao seu aplicativo.
