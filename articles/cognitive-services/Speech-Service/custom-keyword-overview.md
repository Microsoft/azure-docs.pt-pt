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
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802183"
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

## <a name="sample-code"></a>Código de exemplo

O código de amostra para a integração de um modelo de palavra-chave está disponível no GitHub. Estas amostras cobrem a aplicação do cliente para integrar a sua palavra-chave em várias línguas de programação populares.

* [Tutorial: Ativar a voz do seu produto com o Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="tutorial"></a>Tutorial

* Como [criar uma palavra-chave personalizada usando](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)o Speech Studio .
* Como [ativar o seu produto por voz utilizando o SDK do Discurso](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Doutorados de referência

* [Diretrizes de nomeação de palavras-chave personalizadas](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [API de Voz](speech-sdk-reference.md)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
* [Criar uma palavra-chave personalizada](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [Obtenha o SDK do Discurso](speech-sdk.md)
* [Saiba mais sobre assistentes de voz](voice-assistants.md)
