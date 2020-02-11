---
title: O que é o serviço de Voz?
titleSuffix: Azure Cognitive Services
description: O serviço de fala é a Unificação de fala para texto, conversão de texto em fala e tradução de fala em uma única assinatura do Azure. Adicione o discurso às suas aplicações, ferramentas e dispositivos com o SDK de Fala, Dispositivos de Fala SDK ou APIs REST.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 7ddfae430e6aa4ec9549e40c937e5edcfd927f6d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119918"
---
# <a name="what-is-the-speech-service"></a>O que é o serviço de Voz?

O serviço da Fala é a unificação do discurso-a-texto, do texto à fala e da tradução da fala numa única assinatura Azure. É fácil de falar para permitir as suas aplicações, ferramentas e dispositivos com o [SDK de Fala,](speech-sdk-reference.md)Dispositivos de [Fala SDK,](https://aka.ms/sdsdk-quickstart)ou [APIs REST](rest-apis.md).

> [!IMPORTANT]
> O serviço de fala substituiu API de Fala do Bing, Tradução de Fala e Fala Personalizada. Consulte _Como guiar > Migração_ para instruções de migração.

Esses recursos compõem o serviço de fala. Use os links nesta tabela para saber mais sobre casos de uso comuns para cada recurso ou procure a referência de API.

| Serviço | Funcionalidade | Descrição | SDK | REST |
| ------- | ------- | ----------- | --- | ---- |
| [Discurso ao Texto](speech-to-text.md) | Conversão de voz em texto | A fala a texto transcreve fluxos de áudio para texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use o discurso a texto com a compreensão da [linguagem (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar as intenções dos utilizadores de falar transcrito e agir sobre comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transcrição do lote](batch-transcription.md) | A transcrição em lote permite a transcrição de fala em texto assíncrona de grandes volumes de dados. Esse é um serviço baseado em REST, que usa o mesmo ponto de extremidade como personalização e gerenciamento de modelos. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| | [Conversa ção multi-dispositivo](multi-device-conversation.md) | Conecte vários dispositivos ou clientes em uma conversa para enviar mensagens baseadas em fala ou texto, com suporte fácil para transcrição e tradução| Sim | Não |
| | [Transcrição de conversação](conversation-transcription-service.md) | Habilita o reconhecimento de fala em tempo real, a identificação do orador e o diarization. É perfeito para transcrever reuniões presenciais com a capacidade de diferenciar os alto-falantes. | Sim | Não |
| | [Criar modelos de discurso personalizados](#customize-your-speech-experience) | Se estiver usando a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados para resolver o ruído de ambiente ou vocabulário específico do setor. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Texto-a-discurso](text-to-speech.md) | Conversão de texto em voz | Texto a fala converte texto de entrada em discurso sintetizado semelhante ao humano usando linguagem de marcação da síntese da [fala (SSML)](speech-synthesis-markup.md). Escolha entre vozes padrão e vozes neurais (ver [suporte linguístico).](language-support.md) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Criar vozes personalizadas](#customize-your-speech-experience) | Crie fontes de voz personalizadas exclusivas para sua marca ou produto. | Não | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução de Voz](speech-translation.md) | Tradução de voz | A conversão de fala permite tradução de fala em tempo real em vários idiomas para seus aplicativos, ferramentas e dispositivos. Use este serviço para tradução de fala a fala e conversão de fala em texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não |
| [Assistentes de voz](voice-assistants.md) | Assistentes de voz | Os assistentes de voz que usam o serviço de fala capacitam os desenvolvedores a criarem interfaces de conversação naturais e humanas para seus aplicativos e experiências. O serviço assistente de voz fornece uma interação rápida e confiável entre um dispositivo e uma implementação de assistente que usa o canal de fala de linha direta da estrutura de bot ou o serviço de comandos personalizados (versão prévia) integrado para a conclusão da tarefa. | [Sim](voice-assistants.md) | Não |

## <a name="try-the-speech-service"></a>Experimente o serviço de fala

Oferecemos guias de início rápido nas linguagens de programação mais populares, cada uma projetada para que você execute códigos em menos de 10 minutos. Esta tabela contém os guias de início rápido mais populares para cada recurso. Use a navegação do lado esquerdo para explorar idiomas e plataformas adicionais.

| Conversão de fala em texto (SDK) | Conversão de texto em fala (SDK) | Tradução (SDK) |
| -------------------- | -------------------- | ----------------- |
| [Reconhecer o discurso de um ficheiro áudio](quickstarts/speech-to-text-from-file.md) | [Sintetizar o discurso num ficheiro áudio](quickstarts/text-to-speech-audio-file.md) | [Traduzir o discurso para o texto](quickstarts/translate-speech-to-text.md) |
| [Reconhecer discurso com um microfone](quickstarts/speech-to-text-from-microphone.md) | [Sintetizar o discurso a um orador](quickstarts/text-to-speech.md) | [Traduzir o discurso para várias línguas-alvo](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Reconhecer discurso armazenado no armazenamento de bolhas](quickstarts/from-blob.md) | [Síntese de asincronização para áudio de forma longa](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Traduzir o discurso-para-discurso](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> A conversão de fala em texto e a conversão de texto em fala também têm pontos de extremidade REST e guias de início rápido associados.

Depois de ter tido a oportunidade de usar o serviço de Fala, experimente o nosso tutorial que o ensina a reconhecer as intenções da fala usando o SDK da Fala e o LUIS.

- [Tutorial: Reconhecer as intenções do discurso com o Discurso SDK e LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: Voz habilita o seu bot com o SDK de Fala,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutorial: Construa uma aplicação do Flask para traduzir texto, analisar o sentimento e sintetizar texto traduzido para a fala, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obter o código de exemplo

O código de exemplo está disponível no GitHub para o serviço de fala. Estas amostras cobrem cenários comuns como ler áudio de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados. Utilize estes links para visualizar amostras de SDK e REST:

- [Amostras de discurso a texto, texto-a-fala e tradução da fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de texto para a fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalizar sua experiência de fala

No entanto, o serviço de fala funciona bem com modelos internos, você talvez queira personalizar ainda mais e ajustar a experiência para seu produto ou ambiente. As opções de personalização vão desde a sintonização do modelo acústico até fontes de voz únicas para a sua marca.

| Serviço de Voz | Plataforma | Descrição |
| -------------- | -------- | ----------- |
| Voz em Texto | [Discurso Personalizado](https://aka.ms/customspeech) | Personalize modelos de reconhecimento de fala para suas necessidades e dados disponíveis. Superar barreiras de reconhecimento de voz, como o estilo de fala, o vocabulário e o ruído de fundo. |
| Texto em Voz | [Voz Personalizada](https://aka.ms/customvoice) | Crie uma voz única reconhecível para as suas aplicações de Conversão de Texto em Voz com os dados de voz disponíveis. Você pode ajustar ainda mais as saídas de voz ajustando um conjunto de parâmetros de voz. |

## <a name="reference-docs"></a>Doutorados de referência

- [SDK de Voz](speech-sdk-reference.md)
- [Dispositivos de fala SDK](speech-devices-sdk.md)
- [REST API: Discurso-a-texto](rest-speech-to-text.md)
- [REST API: Texto-a-falar](rest-text-to-speech.md)
- [REST API: Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
