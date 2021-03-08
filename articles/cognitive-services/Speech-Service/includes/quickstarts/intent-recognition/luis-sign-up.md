---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 42dc7eb1a74bf2e376f834ee3198ad6a492226e9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444370"
---
Para completar o início rápido do reconhecimento de intenções, terá de criar uma conta LUIS e um projeto utilizando o portal de pré-visualização LUIS. Este arranque rápido requer apenas uma assinatura LUIS. Não é necessária uma subscrição de serviço de *fala.*

A primeira coisa que terá de fazer é criar uma conta LUIS e uma aplicação utilizando o portal de pré-visualização LUIS. A app LUIS que criar utilizará um domínio pré-construído para a domótica, que fornece intenções, entidades e palavras de exemplo. Quando terminar, terá um ponto final LUIS correndo na nuvem que pode chamar usando o Speech SDK. 

Siga estas instruções para criar a sua aplicação LUIS:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Quickstart: Construa uma aplicação de domínio pré-construída </a>

Quando terminar, vai precisar de quatro coisas:

* Re-publicar com **discurso escorva** em
* A sua **chave primária** DA LUIS
* A sua **localização** LUIS
* O seu **ID de aplicativo** LUIS

Aqui é onde você pode encontrar esta informação no [portal de pré-visualização LUIS:](https://preview.luis.ai/)

1. A partir do portal de pré-visualização LUIS, selecione a sua aplicação e, em seguida, selecione o botão **Publicar.**

2. Selecione a ranhura **de Produção,** se estiver a utilizar `en-US` a opção de **escoramento** do Discurso para a posição **On.** Em seguida, selecione o botão **Publicar.**

    > [!IMPORTANT]
    > **A preparação da fala** é altamente recomendada, uma vez que melhorará a precisão do reconhecimento da fala.

    > [!div class="mx-imgBorder"]
    > ![Publicar LUIS para ponto final](../../../media/luis/publish-app-popup.png)

3. A partir do portal de pré-visualização LUIS, **selecione Gerir** e, em seguida, selecione **Recursos Azure**. Nesta página, você encontrará a sua chave e localização LUIS (às vezes referida como _região)._

   > [!div class="mx-imgBorder"]
   > ![Chave e localização LUIS](../../../media/luis/luis-key-region.png)

4. Depois de ter a sua chave e localização, vai precisar da identificação da aplicação. Selecione **Definições de Aplicação** -- o seu ID da aplicação está disponível nesta página.

   > [!div class="mx-imgBorder"]
   > ![ID da aplicação LUIS](../../../media/luis/luis-app-id.png)
