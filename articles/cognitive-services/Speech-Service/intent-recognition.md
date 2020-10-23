---
title: Visão geral do reconhecimento de intenções - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O reconhecimento de intenções permite-lhe reconhecer os objetivos do utilizador que tem pré-definidos. Este artigo é uma visão geral dos benefícios e capacidades do serviço de reconhecimento de intenções.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: reconhecimento de intenção
ms.openlocfilehash: 0d718459e0fd0ea410232d3a165b560aa8c59cd1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174643"
---
# <a name="what-is-intent-recognition"></a>O que é o reconhecimento de intenções?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, aprende-se sobre os benefícios e capacidades de reconhecimento de intenções. O SDK Voz dos Serviços Cognitivos integra-se no serviço Language Understanding (LUIS) para proporcionar o reconhecimento de intenções. Uma intenção é algo que o utilizador quer fazer, seja reservar um voo, ver tempo ou fazer uma chamada.
Utilizando o reconhecimento de intenções, as suas aplicações, ferramentas e dispositivos podem determinar o que o utilizador deseja iniciar ou fazer com base nas opções que define no LUIS.

## <a name="luis-key-required"></a>Chave LUIS necessária

* O LUIS integra-se no serviço Voz para reconhecer as intenções de voz. Não precisa de uma subscrição do serviço Voz, apenas do LUIS.
* O reconhecimento da intenção de fala está integrado com o SDK. Pode utilizar uma chave LUIS com o serviço de Discurso.
* O reconhecimento de intenções através do Discurso SDK é [oferecido num subconjunto de regiões apoiadas pelo LUIS](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#intent-recognition).

## <a name="get-started"></a>Introdução

Veja o [quickstart](quickstarts/intent-recognition.md) para começar com o reconhecimento de intenção.

## <a name="sample-code"></a>Código de exemplo

Código de amostra para reconhecimento de intenções:

* [Início Rápido: utilizar a aplicação Home Automation pré-concebida](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)
* [Reconhecer intenções da fala usando o Discurso SDK para C #](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)
* [Reconhecimento de intenções e outros serviços de fala usando unidade em C #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Reconhecer intenções usando O SDK de Discurso para Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Reconhecimento de intenções e outros serviços de fala utilizando o Speech SDK para C++ no Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Reconhecimento de intenções e outros serviços de fala utilizando o Speech SDK para Java no Windows ou Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Reconhecimento de intenções e outros serviços de fala usando o Speech SDK para JavaScript em um navegador web](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Documentos de referência

* [API de Voz](speech-sdk-reference.md)

## <a name="next-steps"></a>Passos seguintes

* Complete o início rápido do reconhecimento [de intenção](quickstarts/intent-recognition.md)
* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
* [Obtenha o Discurso SDK](speech-sdk.md)
