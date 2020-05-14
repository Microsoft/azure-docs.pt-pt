---
title: Palavras-chave personalizadas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral das funcionalidades, capacidades e restrições para palavras-chave personalizadas usando o Kit de Desenvolvimento de Software de Fala (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 0a6df626f9567b51a8371c17004b454f9c5b4597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202035"
---
# <a name="what-is-a-keyword"></a>O que é uma palavra-chave?

Uma palavra-chave é uma palavra ou frase curta que permite ativar o seu produto por voz. Por exemplo, "Hey Cortana" é a palavra-chave para o assistente cortana. A ativação por voz permite que os seus utilizadores comecem a interagir com o seu produto completamente mãos-livres, simplesmente falando a palavra-chave. À medida que o seu produto ouve continuamente a palavra-chave, todo o áudio é processado localmente no dispositivo do utilizador até que ocorra uma deteção para garantir que os seus dados permanecem o mais privados possível.

## <a name="core-features-of-custom-keyword"></a>Características centrais da palavra-chave personalizada

Com as funcionalidades de personalização, desempenho e integração da Custom Keyword, pode adaptar a ativação de voz para melhor se adequar à visão do seu produto e às necessidades dos utilizadores.

| Funcionalidade | Descrição |
|----------|----------|
| Personalização de palavras-chave | Como extensão da sua marca, uma palavra-chave reforça a equidade que construiu com os seus clientes. O portal Custom Keyword no Speech Studio permite especificar qualquer palavra ou frase curta que melhor represente a sua marca. Pode personalizar ainda mais a sua palavra-chave escolhendo as pronúncias certas, que serão homenageadas pelo modelo de palavra-chave gerado.
| Verificação de palavras-chave | Quando há alta confiança na palavra-chave que está a ser detetada localmente, o áudio é enviado para a nuvem para mais verificação de que um utilizador disse a palavra-chave. A verificação das palavras-chave fornece uma camada adicional de segurança reduzindo o impacto de uma deteção local incorreta e protegendo a privacidade do utilizador.
| Assistente de voz & integração do SDK do Discurso | As palavras-chave geradas a partir da palavra-chave personalizada no Estúdio de Discurso podem ser facilmente integradas dentro do seu dispositivo ou aplicação através do Speech SDK. Basta apontar o SDK para o modelo de palavra-chave fornecido pelo Speech Studio e o seu produto será ativado por voz, apoiado pela verificação de palavras-chave. Pode completar as experiências de voz do seu produto construindo o seu próprio assistente de [voz.](voice-assistants.md)

## <a name="get-started-with-custom-keywords"></a>Começar com palavras-chave personalizadas

* Tutorial: Como [criar uma palavra-chave personalizada usando](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) o Speech Studio
* Tutorial: Como [ativar o seu produto com o SDK da Fala, utilizando C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* Quickstart: [Reconheça palavras-chave com o SDK de Discurso, na Plataforma Universal Windows usando C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* Quickstart: [Reconheça palavras-chave com o SDK de Discurso, no Android usando Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Próximos passos

* [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
* [Obtenha o SDK do Discurso](speech-sdk.md)
* [Saiba mais sobre assistentes de voz](voice-assistants.md)
