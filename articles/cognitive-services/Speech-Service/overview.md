---
title: O que é o serviço de Voz?
titleSuffix: Azure Cognitive Services
description: O serviço da Fala é a unificação da fala-a-texto, do texto à fala e da tradução da fala numa única assinatura Azure. Adicione o discurso às suas aplicações, ferramentas e dispositivos com o SDK de Fala, Dispositivos de Fala SDK ou APIs REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401026"
---
# <a name="what-is-the-speech-service"></a>O que é o serviço de Voz?

O serviço da Fala é a unificação do discurso-a-texto, do texto à fala e da tradução da fala numa única assinatura Azure. É fácil de falar para permitir as suas aplicações, ferramentas e dispositivos com o [SDK de Fala,](speech-sdk-reference.md)Dispositivos de [Fala SDK,](https://aka.ms/sdsdk-quickstart)ou [APIs REST](rest-apis.md).

> [!IMPORTANT]
> O serviço de Discurso substituiu a API do Discurso de Bing e o Discurso do Tradutor. Consulte _como orientar > Migração_ para instruções de migração.

Estas características compõem o serviço de Fala. Utilize os links nesta tabela para saber mais sobre casos de uso comum para cada recurso ou navegue na referência DaPI.

| Serviço | Funcionalidade | Descrição | SDK | REST |
|---------|---------|-------------|-----|------|
| [Discurso ao Texto](speech-to-text.md) | Discurso-a-texto em tempo real | O discurso-a-texto transcreve ou traduz streams de áudio ou ficheiros locais para texto em tempo real que as suas aplicações, ferramentas ou dispositivos possam consumir ou exibir. Use o discurso a texto com a compreensão da [linguagem (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar as intenções dos utilizadores de falar transcrito e agir sobre comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Lote Discurso ao Texto](batch-transcription.md) | O lote De fala a texto permite a transcrição assíncrona da fala-a-texto de grandes volumes de dados áudio da fala armazenados no Armazenamento de Blob Azure. Além de converter áudio da fala em texto, o Batch Speech-to-text também permite a diarização e a análise de sentimentos. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| | [Conversa ção multi-dispositivo](multi-device-conversation.md) | Conecte vários dispositivos ou clientes numa conversa para enviar mensagens baseadas em discursos ou textos, com suporte fácil para transcrição e tradução| Sim | Não |
| | [Transcrição de conversação](conversation-transcription-service.md) | Permite o reconhecimento da fala em tempo real, a identificação dos altifalantes e a diarização. É perfeito para transcrever reuniões presenciais com a capacidade de distinguir os falantes. | Sim | Não |
| | [Criar modelos de discurso personalizados](#customize-your-speech-experience) | Se estiver a usar o discurso ao texto para reconhecimento e transcrição num ambiente único, pode criar e formar modelos acústicos, linguísticos e de pronúncia personalizados para abordar o ruído ambiente ou o vocabulário específico da indústria. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Texto-a-discurso](text-to-speech.md) | Conversão de texto em voz | Texto a fala converte texto de entrada em discurso sintetizado semelhante ao humano usando linguagem de marcação da síntese da [fala (SSML)](speech-synthesis-markup.md). Escolha entre vozes padrão e vozes neurais (ver [suporte linguístico).](language-support.md) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Criar vozes personalizadas](#customize-your-speech-experience) | Crie fontes de voz personalizadas únicas à sua marca ou produto. | Não | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução da Fala](speech-translation.md) | Tradução de voz | A tradução da fala permite a tradução da fala em tempo real e multi-linguagem para as suas aplicações, ferramentas e dispositivos. Utilize este serviço para a tradução fala-a-fala e de fala a texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não |
| [Assistentes de voz](voice-assistants.md) | Assistentes de voz | Assistentes de voz que usam o serviço Speech capacitam os desenvolvedores a criar interfaces de conversação naturais e humanas para as suas aplicações e experiências. O serviço de assistente de voz proporciona uma interação rápida e fiável entre um dispositivo e uma implementação de assistente que utiliza o canal de discurso de linha direta do Bot Framework ou o serviço integrado de Comandos Personalizados (Pré-visualização) para a conclusão da tarefa. | [Sim](voice-assistants.md) | Não |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Experimente o serviço de Discurso

Oferecemos quickstarts nas línguas de programação mais populares, cada uma projetada para tê-lo executando código em menos de 10 minutos. Esta tabela contém os quickstarts mais populares para cada recurso. Utilize a navegação à esquerda para explorar idiomas e plataformas adicionais.

| Discurso a texto (SDK) | Texto-a-fala (SDK) | Tradução (SDK) |
|----------------------|----------------------|-------------------|
| [Reconhecer a voz a partir de um ficheiro de áudio](quickstarts/speech-to-text-from-file.md) | [Sintetizar a voz num ficheiro de áudio](quickstarts/text-to-speech-audio-file.md) | [Traduzir o discurso para o texto](quickstarts/translate-speech-to-text.md) |
| [Reconhecer a voz com um microfone](quickstarts/speech-to-text-from-microphone.md) | [Sintetizar a voz num altifalante](quickstarts/text-to-speech.md) | [Traduzir voz para vários idiomas de destino](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Reconhecer a voz armazenada no armazenamento de blobs](quickstarts/from-blob.md) | [Sintetização assíncrona para áudio de formato longo](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Traduzir voz para voz](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> O discurso-a-texto e o texto-a-fala também têm pontos finais REST e quickstarts associados.

Depois de ter tido a oportunidade de usar o serviço de Fala, experimente o nosso tutorial que o ensina a reconhecer as intenções da fala usando o SDK da Fala e o LUIS.

- [Tutorial: Reconhecer as intenções do discurso com o Discurso SDK e LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: Voz habilita o seu bot com o SDK de Fala, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutorial: Construa uma aplicação do Flask para traduzir texto, analisar o sentimento e sintetizar texto traduzido para a fala, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obter o código de exemplo

O código da amostra está disponível no GitHub para o serviço de Fala. Estas amostras cobrem cenários comuns como ler áudio de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados. Utilize estes links para visualizar amostras de SDK e REST:

- [Amostras de discurso a texto, texto-a-fala e tradução da fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de texto para a fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalize a sua experiência de fala

O serviço De Fala funciona bem com modelos incorporados, no entanto, você pode querer personalizar e afinar ainda mais a experiência para o seu produto ou ambiente. As opções de personalização vão desde a sintonização do modelo acústico até fontes de voz únicas para a sua marca.

| Serviço de Voz | Plataforma | Descrição |
| -------------- | -------- | ----------- |
| Voz em Texto | [Discurso Personalizado](https://aka.ms/customspeech) | Personalize os modelos de reconhecimento de voz às suas necessidades e dados disponíveis. Superar barreiras de reconhecimento de voz, como o estilo de fala, o vocabulário e o ruído de fundo. |
| Texto em Voz | [Voz Personalizada](https://aka.ms/customvoice) | Crie uma voz única reconhecível para as suas aplicações de Conversão de Texto em Voz com os dados de voz disponíveis. Pode afinar ainda mais as saídas de voz ajustando um conjunto de parâmetros de voz. |

## <a name="reference-docs"></a>Doutorados de referência

- [API de Voz](speech-sdk-reference.md)
- [SDK de Dispositivos de Voz](speech-devices-sdk.md)
- [REST API: Discurso-a-texto](rest-speech-to-text.md)
- [REST API: Texto-a-falar](rest-text-to-speech.md)
- [REST API: Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
