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
ms.custom: devx-track-csharp
ms.openlocfilehash: b2763372cc63637f73610e1936e3ea54a4524e7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91356609"
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

## <a name="choose-an-effective-keyword"></a>Escolha uma palavra-chave eficaz

A criação de uma palavra-chave eficaz é vital para garantir que o seu dispositivo irá responder de forma consistente e precisa. Personalizar a sua palavra-chave é uma forma eficaz de diferenciar o seu dispositivo e fortalecer a sua marca. Considere as seguintes diretrizes quando escolher uma palavra-chave:

> [!div class="checklist"]
> * A sua palavra-chave deve ser uma palavra ou frase em inglês.
> * Não deve levar mais do que dois segundos para dizer.
> * Palavras de 4 a 7 sílabas funcionam melhor. Por exemplo, "Hey, Computador" é uma boa palavra-chave. Só "Hey" é um pobre.
> * As palavras-chave devem seguir as regras comuns de pronúncia inglesa.
> * Uma palavra única ou mesmo inventada que segue regras comuns de pronúncia inglesa pode reduzir falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.
> * Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas dizem frequentemente em conversas comuns. Podem ser falsos gatilhos para o seu dispositivo.
> * Evite utilizar uma palavra-chave que possa ter pronúncias alternativas. Os utilizadores teriam de saber a pronúncia "certa" para que o seu dispositivo respondesse. Por exemplo, "509" pode ser pronunciado "cinco zero nove", "cinco oh nove", ou "quinhentos e nove". "R.E.I." pode ser pronunciado "r-e-i" ou "ray". "Live" pode ser pronunciado "/līv/" ou "/liv/".
> * Não utilize caracteres, símbolos ou dígitos especiais. Por exemplo, "Go#" e "20 + gatos" podem ser palavras-chave problemáticas. No entanto, "ir afiado" ou "vinte mais gatos" pode funcionar. Você ainda pode usar os símbolos na sua marca e usar marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se escolher uma palavra registada como palavra-chave, certifique-se de que é proprietária dessa marca ou de que tem permissão do proprietário da marca para usar a palavra. A Microsoft não se responsabiliza por quaisquer problemas legais que possam surgir da sua escolha de palavras-chave.

## <a name="see-samples-on-github"></a>Ver amostras no GitHub

* [Reconhecer palavras-chave com o SDK de discurso, na Plataforma Universal windows usando C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Reconhecer palavras-chave com o Speech SDK, no Android usando Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
* [Obtenha o Discurso SDK](speech-sdk.md)
* [Saiba mais sobre Assistentes de Voz](voice-assistants.md)
