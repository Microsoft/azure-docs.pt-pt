---
title: Imagens moderadas com a Consola API - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a API de Moderação de Imagem no Moderador de Conteúdo Azure para iniciar fluxos de trabalho de moderação de digitalização e revisão para conteúdos de imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 542fadd5e5ab91be7b7113064bf8c998dae08d12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912944"
---
# <a name="moderate-images-from-the-api-console"></a>Imagens moderadas da consola API

Utilize a [API de Moderação de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) no Moderador de Conteúdo Azure para iniciar fluxos de trabalho de moderação de digitalização e revisão para conteúdos de imagem. O trabalho de moderação digitaliza o seu conteúdo por profanação, e compara-o com listas de bloqueios personalizadas e partilhadas.

## <a name="use-the-api-console"></a>Utilize a consola API
Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Isto está localizado no **separador Definições,** na caixa **Ocp-Apim-Subscription-Key.** Para obter mais informações, veja [Descrição geral](overview.md).

1. Vá à [referência API de Moderação de Imagem.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)

   A **página de** moderação de imagem abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Tente imagem - Avaliar a seleção da região da página](images/test-drive-region.png)
  
   A **imagem - Avaliar a** consola API abre.

3. Na caixa **Ocp-Apim-Subscription-Key,** insira a sua chave de subscrição.

   ![Tente imagem - Avalie a chave de subscrição da consola](images/try-image-api-1.PNG)

4. Na caixa **corporal Request,** utilize a imagem da amostra predefinida ou especifique uma imagem para digitalizar. Pode submeter a imagem em si como dados binários de bits ou especificar um URL acessível ao público para uma imagem. 

   Para este exemplo, utilize o caminho previsto na caixa **corporal Request** e, em seguida, selecione **Enviar**. 

   ![Tente imagem - Avalie o corpo do pedido da consola](images/try-image-api-2.PNG)

   Esta é a imagem naquele URL:

   ![Experimente imagem - Avalie a imagem da amostra da consola](images/sample-image.jpg) 

5. Selecione **Enviar**.

6. A API devolve uma pontuação de probabilidade para cada classificação. Também devolve a determinação de se a imagem satisfaz as condições (**verdadeiras** ou **falsas).** 

   ![Tente imagem - Avalie a pontuação da probabilidade da consola e a determinação da condição](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Deteção de rostos

Pode utilizar a API de Moderação de Imagem para localizar rostos numa imagem. Esta opção pode ser útil quando tem preocupações de privacidade e pretende evitar que um rosto específico seja publicado na sua plataforma. 

1. Na [referência API de Moderação de Imagem,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)no menu esquerdo, em **Imagem,** selecione **Find Faces**. 

   A página **Image - Find Faces** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Experimente a imagem - Encontre a seleção da região da página Faces](images/test-drive-region.png)

   A consola **Image - Find Faces** API abre.

3. Especifique uma imagem para digitalizar. Pode submeter a imagem em si como dados binários de bits ou especificar um URL acessível ao público para uma imagem. Este exemplo liga-se a uma imagem que é usada numa história da CNN.

   ![Experimente imagem - Encontre rostos imagem de amostra](images/try-image-api-face-image.jpg)

   ![Experimente imagem - Encontre o pedido de amostra de Faces](images/try-image-api-face-request.png)

4. Selecione **Enviar**. Neste exemplo, a API encontra duas faces e devolve as suas coordenadas na imagem.

   ![Experimente imagem - Encontre rostos amostra Resposta caixa de conteúdo](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Deteção de texto através da capacidade de OCR

Pode utilizar a capacidade de OCR do Moderador de Conteúdo para detetar texto em imagens.

1. Na [referência API de Moderação de Imagem,](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)no menu esquerdo, em **Imagem,** selecione **OCR**. 

   A **página Imagem - OCR** abre.

2. Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

   ![Imagem - Seleção da região da página de OCR](images/test-drive-region.png)

   A consola **Image - OCR** API abre.

3. Na caixa **Ocp-Apim-Subscription-Key,** insira a sua chave de subscrição.

4. Na caixa **de corpo Request,** utilize a imagem de amostra predefinido. Esta é a mesma imagem que é usada na secção anterior.

5. Selecione **Enviar**. O texto extraído é apresentado em JSON:

   ![Imagem - Caixa de conteúdo de resposta à amostra de OCR](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou siga o [quickstart .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) para adicionar moderação de imagem à sua aplicação.