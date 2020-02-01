---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: 58122066e65fbcb02b6b4333985785b219d3dbbd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900603"
---
Para concluir o início rápido do reconhecimento de intenção, você precisará criar uma conta do LUIS e um projeto usando o portal de visualização do LUIS. Este início rápido requer apenas uma assinatura LUIS. Não *é* necessária uma subscrição do serviço de fala.

A primeira coisa que você precisará fazer é criar uma conta e um aplicativo do LUIS usando o portal de visualização do LUIS. O aplicativo LUIS que você cria usará um domínio predefinido para a automação inicial, que fornece intenções, entidades e exemplos de declarações. Quando tiver terminado, você terá um ponto de extremidade LUIS em execução na nuvem que você pode chamar usando o SDK de fala. 

Siga estas instruções para criar seu aplicativo LUIS:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Quickstart: Construir app de domínio pré-construído<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Quando terminar, vai precisar de quatro coisas:

* Re-publicar com **discurso priming** toggled on
* A sua **chave primária** LUIS
* A sua **localização** LUIS
* O seu ID de **aplicativo** LUIS

Aqui está onde você pode encontrar essas informações no [portal de visualização do Luis](https://preview.luis.ai/):

1. A partir do portal de pré-visualização LUIS, selecione a sua aplicação e, em seguida, selecione o botão **Publicar.**

2. Selecione a ranhura **de Produção,** se estiver a utilizar `en-US` alternar a opção de **preparação do Discurso** para a posição **On.** Em seguida, selecione o botão **Publicar.**

    > [!IMPORTANT]
    > **A preparação da fala** é altamente recomendada, uma vez que melhorará a precisão do reconhecimento da fala.

    > [!div class="mx-imgBorder"]
    > ![Publicar LUIS para](../../../media/luis/publish-app-popup.png) final

3. No portal de visualização do LUIS, selecione **gerenciar**e, em seguida, selecione **recursos do Azure**. Nessa página, você encontrará sua chave LUIS e o local (às vezes chamado de _região_).

   > [!div class="mx-imgBorder"]
   > ![chave LUIS e](../../../media/luis/luis-key-region.png) de localização

4. Depois de obter a chave e o local, você precisará da ID do aplicativo. Selecione **configurações do aplicativo** – a ID do aplicativo está disponível nesta página.

   > [!div class="mx-imgBorder"]
   > ![app](../../../media/luis/luis-app-id.png)
