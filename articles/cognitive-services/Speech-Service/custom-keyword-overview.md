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
ms.openlocfilehash: 60bcdac0d7e19c424b007980294898638814d586
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044865"
---
# <a name="what-is-a-keyword"></a>O que é uma palavra-chave?

Uma palavra-chave é uma palavra ou frase curta que permite ativar a voz do seu produto. Por exemplo, "Hey Cortana" é a palavra-chave para o assistente cortana. A ativação por voz permite que os seus utilizadores comecem a interagir com o seu produto completamente mãos-livres, bastando para dizer a palavra-chave. À medida que o seu produto escuta continuamente a palavra-chave, todo o áudio é processado localmente no dispositivo do utilizador até que ocorra uma deteção para garantir que os seus dados permanecem o mais privados possível.

## <a name="core-features-of-custom-keyword"></a>Principais características da palavra-chave personalizada

Com as funcionalidades de personalização, desempenho e integração da Palavra-Chave Personalizada, pode adaptar a ativação de voz de melhor forma à visão do seu produto e às necessidades dos utilizadores.

| Funcionalidade | Descrição |
|----------|----------|
| Personalização de palavras-chave | Como extensão da sua marca, uma palavra-chave reforça o capital próprio que construiu com os seus clientes. O portal de palavras-chave personalizado no Speech Studio permite-lhe especificar qualquer palavra ou frase curta que melhor represente a sua marca. Pode personalizar ainda mais a sua palavra-chave escolhendo as pronúncias certas, que serão honradas pelo modelo de palavra-chave gerado.
| Verificação de palavras-chave | Quando há uma grande confiança na palavra-chave que está a ser detetada localmente, o áudio é enviado para a nuvem para posterior verificação que um utilizador disse a palavra-chave. A verificação de palavras-chave proporciona uma camada adicional de segurança reduzindo o impacto de uma deteção local incorreta e protegendo a privacidade do utilizador.
| Assistente de voz & integração SDK do discurso | As palavras-chave geradas a partir da palavra-chave personalizada no Speech Studio podem ser facilmente integradas no seu dispositivo ou aplicação através do SDK de discurso. Basta apontar o SDK para o modelo de palavras-chave fornecido pelo Speech Studio e o seu produto será ativado por voz, apoiado pela verificação de palavras-chave. Pode completar as experiências de voz do seu produto construindo o seu próprio [assistente de voz.](voice-assistants.md)

## <a name="get-started-with-custom-keywords"></a>Começar com palavras-chave personalizadas

* Consulte [as palavras-chave personalizadas](custom-keyword-basics.md) para padrões básicos de utilização e design.
* Como [ativar o seu produto por voz com o Speech SDK, utilizando C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="see-samples-on-github"></a>Ver amostras no GitHub

* [Reconhecer palavras-chave com o SDK de discurso, na Plataforma Universal windows usando C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Reconhecer palavras-chave com o Speech SDK, no Android usando Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](get-started.md)
* [Obtenha o Discurso SDK](speech-sdk.md)
* [Saiba mais sobre Assistentes de Voz](voice-assistants.md)
