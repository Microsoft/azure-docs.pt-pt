---
title: 'Início rápido: reconhecer fala, intenções e entidades, serviço de fala em Python'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660579"
---
Para concluir o início rápido do reconhecimento de intenção, você precisará criar uma conta do LUIS e um projeto usando o portal de visualização do LUIS. Este início rápido requer apenas uma assinatura LUIS. Uma assinatura do serviço de fala não é necessária.

A primeira coisa que você precisará fazer é criar uma conta e um aplicativo do LUIS usando o portal de visualização do LUIS. O aplicativo LUIS que você cria usará um domínio predefinido para a automação inicial, que fornece intenções, entidades e exemplos de declarações. Quando tiver terminado, você terá um ponto de extremidade LUIS em execução na nuvem que você pode chamar usando o SDK de fala. 

Siga estas instruções para criar seu aplicativo LUIS: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Início rápido: Compilar aplicativo de domínio predefinido</a>

Quando terminar, você precisará de três itens: 

* Sua chave LUIS
* Sua região LUIS
* Sua ID do aplicativo LUIS

Aqui está onde você pode encontrar essas informações no [portal de visualização do Luis](https://preview.luis.ai/):

1. No portal de visualização do LUIS, selecione **gerenciar**e, em seguida, selecione **recursos do Azure**. Nessa página, você encontrará sua chave LUIS e o local (às vezes chamado de _região_).  

   > [!div class="mx-imgBorder"]
   > ![chave e local de LUIS](../../../media/luis/luis-key-region.png)

2. Depois de obter a chave e o local, você precisará da ID do aplicativo. Selecione **configurações do aplicativo** – a ID do aplicativo está disponível nesta página.

   > [!div class="mx-imgBorder"]
   > ![ID do aplicativo LUIS](../../../media/luis/luis-app-id.png)