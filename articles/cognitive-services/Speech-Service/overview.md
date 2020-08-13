---
title: O que é o serviço de Voz?
titleSuffix: Azure Cognitive Services
description: O serviço de discurso é a unificação da tradução de discurso para texto, texto-a-fala e tradução da fala numa única subscrição do Azure. Adicione a fala às suas aplicações, ferramentas e dispositivos com o SDK de fala, dispositivos de fala SDK ou APIs REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 82099172a933496f015ae8fc575c1919a879e1f9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167747"
---
# <a name="what-is-the-speech-service"></a>O que é o serviço de Voz?

O serviço de discurso é a unificação da palavra-a-texto, texto-a-discurso e tradução da fala numa única subscrição do Azure. É fácil de falar para ativar as suas aplicações, ferramentas e dispositivos com o [Speech CLI](spx-overview.md), [Speech SDK,](speech-sdk-reference.md) [Speech Devices SDK,](https://aka.ms/sdsdk-quickstart) [Speech Studio](https://speech.microsoft.com/)ou [REST APIs](rest-apis.md).

> [!IMPORTANT]
> O serviço de discurso substituiu a API de Discurso de Bing e o Discurso do Tradutor. Consulte _os guias > Migração_ para instruções de migração.

Estas funcionalidades compõem o serviço de Discurso. Utilize os links desta tabela para saber mais sobre casos de uso comum para cada recurso ou navegue na referência API.

| Serviço | Funcionalidade | Descrição | SDK | REST |
|---------|---------|-------------|-----|------|
| [Discurso-a-Texto](speech-to-text.md) | Discurso-a-texto em tempo real | A palavra-para-texto transcreve ou traduz streams de áudio ou ficheiros locais para texto em tempo real que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. Use o discurso-a-texto com [a Compreensão da Linguagem (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) para obter as intenções dos utilizadores da fala transcrita e agir em comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Lote Discurso-a-Texto](batch-transcription.md) | Lote O discurso-a-texto permite a transcrição assíncrona de discurso a texto de grandes volumes de dados áudio da fala armazenados no Azure Blob Storage. Além de converter áudio de fala em texto, o Batch Speech-to-text também permite a diarização e a análise de sentimentos. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| | [Conversa multi-dispositivo](multi-device-conversation.md) | Conecte vários dispositivos ou clientes numa conversação para enviar mensagens baseadas em discursos ou textos, com suporte fácil para transcrição e tradução| Sim | Não |
| | [Transcrição de conversa](conversation-transcription-service.md) | Permite o reconhecimento da fala em tempo real, a identificação e a diarização dos altifalantes. É perfeito para transcrever reuniões presenciais com a capacidade de distinguir os falantes. | Sim | Não |
| | [Criar modelos de discurso personalizados](#customize-your-speech-experience) | Se estiver a usar texto-a-texto para reconhecimento e transcrição num ambiente único, pode criar e formar modelos acústicos, linguísticos e de pronúncia personalizados para abordar o ruído ambiente ou o vocabulário específico da indústria. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Texto-a-Discurso](text-to-speech.md) | Conversão de texto em voz | O texto-a-discurso converte o texto de entrada em linguagem humana sintetizada utilizando [a linguagem de marcação da síntese de fala (SSML)](speech-synthesis-markup.md). Escolha entre vozes padrão e vozes neurais (ver [suporte linguístico).](language-support.md) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Criar vozes personalizadas](#customize-your-speech-experience) | Crie fontes de voz personalizadas únicas à sua marca ou produto. | Não | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução de Voz](speech-translation.md) | Tradução de voz | A tradução da fala permite a tradução em tempo real, multi-linguístico da fala para as suas aplicações, ferramentas e dispositivos. Utilize este serviço para tradução fala-a-fala e discurso-a-texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não |
| [Assistentes de voz](voice-assistants.md) | Assistentes de voz | Os assistentes de voz que usam o serviço Speech capacitam os desenvolvedores para criar interfaces de conversação naturais e humanas para as suas aplicações e experiências. O serviço de assistente de voz proporciona uma interação rápida e fiável entre um dispositivo e uma implementação de assistente que utiliza o canal de Fala de Linha Direta do Quadro Bot ou o serviço de Comandos Personalizados integrados (Pré-visualização) para a conclusão da tarefa. | [Sim](voice-assistants.md) | Não |
| [Reconhecimento de Orador](speaker-recognition-overview.md) | Verificação de & de verificação de altifalantes | O serviço de Reconhecimento de Altifalantes fornece algoritmos que verificam e identificam os altifalantes pelas suas características de voz únicas. O Reconhecimento dos Oradores é usado para responder à pergunta "quem está a falar?". | Sim | [Sim](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Experimente o serviço de discurso

Oferecemos quickstarts nas linguagens de programação mais populares, cada uma projetada para ter o seu código de execução em menos de 10 minutos. Esta tabela contém os quickstarts mais populares para cada recurso. Use a navegação à esquerda para explorar idiomas e plataformas adicionais.

| Discurso-a-texto (SDK) | Texto-a-Discurso (SDK) | Tradução (SDK) |
|----------------------|----------------------|-------------------|
| [Reconhecer a voz a partir de um ficheiro de áudio](quickstarts/speech-to-text-from-file.md) | [Sintetizar a voz num ficheiro de áudio](quickstarts/text-to-speech-audio-file.md) | [Traduzir discurso para texto](quickstarts/translate-speech-to-text.md) |
| [Reconhecer a voz com um microfone](quickstarts/speech-to-text-from-microphone.md) | [Sintetizar a voz num altifalante](quickstarts/text-to-speech.md) | [Traduzir voz para vários idiomas de destino](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Reconhecer a voz armazenada no armazenamento de blobs](quickstarts/from-blob.md) | [Sintetização assíncrona para áudio de formato longo](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Traduzir voz para voz](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Falar para texto e texto-a-discurso também têm pontos finais REST e quickstarts associados.

Depois de ter tido a oportunidade de usar o serviço de Fala, experimente os nossos tutoriais que o ensinam a resolver vários cenários.

- [Tutorial: Reconhecer intenções do discurso com o Discurso SDK e LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: A voz permite o seu bot com o Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutorial: Construir uma app Flask para traduzir texto, analisar sentimentos e sintetizar texto traduzido para a fala, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obter o código de exemplo

O código de amostra está disponível no GitHub para o serviço Speech. Estas amostras cobrem cenários comuns como ler áudio a partir de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados. Utilize estes links para visualizar amostras de SDK e REST:

- [Amostras de tradução de texto e fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de texto para falar (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalize a sua experiência de fala

O serviço Speech funciona bem com modelos incorporados, no entanto, é melhor personalizar e afinar a experiência para o seu produto ou ambiente. As opções de personalização vão desde a sintonização de modelos acústicos a fontes de voz únicas para a sua marca.

| Serviço de Voz | Plataforma | Descrição |
| -------------- | -------- | ----------- |
| Voz em Texto | [Discurso personalizado](https://aka.ms/customspeech) | Personalize os modelos de reconhecimento de voz às suas necessidades e dados disponíveis. Supere barreiras de reconhecimento de voz, tais como estilo de fala, vocabulário e ruído de fundo. |
| Texto em Voz | [Voz Personalizada](https://aka.ms/customvoice) | Crie uma voz única reconhecível para as suas aplicações de Conversão de Texto em Voz com os dados de voz disponíveis. Pode afinar ainda mais as saídas de voz ajustando um conjunto de parâmetros de voz. |

## <a name="reference-docs"></a>Documentos de referência

- [API de Voz](speech-sdk-reference.md)
- [SDK de Dispositivos de Voz](speech-devices-sdk.md)
- [REST API: Discurso-a-texto](rest-speech-to-text.md)
- [REST API: Texto-a-discurso](rest-text-to-speech.md)
- [REST API: Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](get-started.md)
