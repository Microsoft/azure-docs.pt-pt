---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a41c9dc888e85baf021712e3302da7ae6106db47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422046"
---
Para completar o reconhecimento rápido da intenção, terá de criar uma conta LUIS e um projeto utilizando o portal de pré-visualização LUIS. Este arranque rápido requer apenas uma assinatura LUIS. Não *é* necessária uma subscrição do serviço de fala.

A primeira coisa que terá de fazer é criar uma conta E app LUIS utilizando o portal de pré-visualização LUIS. A app LUIS que criar utilizará um domínio pré-construído para a automação domiciliária, que fornece intenções, entidades e declarações de exemplo. Quando terminar, terá um ponto final luis correndo na nuvem a que pode chamar usando o Speech SDK. 

Siga estas instruções para criar a sua aplicação LUIS:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Quickstart: Construir app de domínio pré-construído<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Quando terminar, vai precisar de quatro coisas:

* Re-publicar com **discurso priming** toggled on
* A sua **chave primária** LUIS
* A sua **localização** LUIS
* O seu ID de **aplicativo** LUIS

Aqui é onde você pode encontrar esta informação no portal de [pré-visualização LUIS:](https://preview.luis.ai/)

1. A partir do portal de pré-visualização LUIS, selecione a sua aplicação e, em seguida, selecione o botão **Publicar.**

2. Selecione a ranhura **de Produção,** se estiver a utilizar `en-US` a opção de **preparação** do Discurso para a posição **On.** Em seguida, selecione o botão **Publicar.**

    > [!IMPORTANT]
    > **A preparação da fala** é altamente recomendada, uma vez que melhorará a precisão do reconhecimento da fala.

    > [!div class="mx-imgBorder"]
    > ![Publicar LUIS ao ponto final](../../../media/luis/publish-app-popup.png)

3. A partir do portal de pré-visualização LUIS, selecione **Gerir,** em seguida, selecione **Recursos Azure**. Nesta página, encontrará a sua chave e localização LUIS (por vezes referida como _região)._

   > [!div class="mx-imgBorder"]
   > ![Chave e localização LUIS](../../../media/luis/luis-key-region.png)

4. Depois de ter a sua chave e localização, vai precisar da identificação da aplicação. Selecione Definições de **Aplicação** -- o ID da aplicação está disponível nesta página.

   > [!div class="mx-imgBorder"]
   > ![ID da aplicação LUIS](../../../media/luis/luis-app-id.png)
