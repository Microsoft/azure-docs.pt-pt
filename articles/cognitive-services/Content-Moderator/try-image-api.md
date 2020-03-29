---
title: Imagens moderadas com a Consola API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a API de moderação de imagem em Moderador de Conteúdo Azure para iniciar fluxos de trabalho de moderação de digitalização e revisão para conteúdos de imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75448249"
---
# <a name="moderate-images-from-the-api-console"></a>Imagens moderadas da consola API

Utilize a API de [moderação](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) de imagem em Moderador de Conteúdo Azure para iniciar fluxos de trabalho de moderação de digitalização e revisão para conteúdos de imagem. O trabalho de moderação digitaliza o seu conteúdo para profanação, e compara-o com listas de bloqueios personalizadas e partilhadas.

## <a name="use-the-api-console"></a>Utilize a consola API
Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Isto está localizado no separador **Definições,** na caixa **De-Assinatura-Chave Ocp-Apim..** Para obter mais informações, veja [Descrição geral](overview.md).

1. Ir à [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)de moderação de imagem.

   A **página de imagem - Avaliar** a moderação da imagem abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Experimente imagem - Avaliar a seleção da região da página](images/test-drive-region.png)
  
   A **imagem - Avaliar** a consola API abre.

3. Na caixa **Ocp-Apim-Subscription-Key,** introduza a sua chave de subscrição.

   ![Experimente a Imagem - Avaliar a chave de subscrição da consola](images/try-image-api-1.PNG)

4. Na caixa **de corpo de Pedido,** utilize a imagem da amostra predefinida ou especifique uma imagem para digitalizar. Pode submeter a imagem em si como dados de bits binários, ou especificar um URL acessível ao público para uma imagem. 

   Para este exemplo, utilize o caminho fornecido na caixa **de corpo solicitar** e, em seguida, selecione **Enviar**. 

   ![Experimente imagem - Avaliar o corpo de pedido da consola](images/try-image-api-2.PNG)

   Esta é a imagem do URL:

   ![Experimente imagem - Avaliar a imagem da amostra da consola](images/sample-image.jpg) 

5. Selecione **Enviar**.

6. A API devolve uma pontuação de probabilidade para cada classificação. Também devolve a determinação de se a imagem satisfaz as condições **(verdadeiras** ou **falsas).** 

   ![Experimente imagem - Avaliar a pontuação da probabilidade da consola e a determinação da condição](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Deteção de rostos

Pode utilizar a API de moderação de imagem para localizar rostos numa imagem. Esta opção pode ser útil quando tem preocupações de privacidade e pretende evitar que um rosto específico seja publicado na sua plataforma. 

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)de moderação de imagem, no menu esquerdo, em **Imagem,** selecione **Encontrar Rostos**. 

   A página **Image - Find Faces** abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Experimente imagem - Encontrar rostos seleção da região](images/test-drive-region.png)

   A consola **Image - Find Faces** API abre.

3. Especifique uma imagem para digitalizar. Pode submeter a imagem em si como dados de bits binários, ou especificar um URL acessível ao público para uma imagem. Este exemplo liga-se a uma imagem que é usada numa história da CNN.

   ![Experimente imagem - Encontre rostos imagem de amostra](images/try-image-api-face-image.jpg)

   ![Experimente imagem - Encontre pedido de amostra de rostos](images/try-image-api-face-request.png)

4. Selecione **Enviar**. Neste exemplo, a API encontra duas faces e devolve as suas coordenadas na imagem.

   ![Experimente a imagem - Encontre faces amostra Caixa de conteúdo resposta](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Deteção de texto através da capacidade de OCR

Pode utilizar a capacidade oCR moderador de conteúdo para detetar texto em imagens.

1. Na [referência API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)de moderação de imagem, no menu esquerdo, em **Imagem,** selecione **OCR**. 

   A página **Image - OCR** abre.

2. Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

   ![Imagem - Seleção da região da página OCR](images/test-drive-region.png)

   A consola **Image - OCR** API abre.

3. Na caixa **Ocp-Apim-Subscription-Key,** introduza a sua chave de subscrição.

4. Na caixa **de corpo solicitar,** utilize a imagem da amostra predefinida. Esta é a mesma imagem que é usada na secção anterior.

5. Selecione **Enviar**. O texto extraído é apresentado em JSON:

   ![Imagem - Caixa de conteúdo de resposta da amostra OCR](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou siga o [quickstart .NET SDK](dotnet-sdk-quickstart.md) para adicionar moderação de imagem à sua aplicação.
