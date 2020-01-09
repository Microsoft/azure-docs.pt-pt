---
title: O que é o serviço de Voz?
titleSuffix: Azure Cognitive Services
description: O serviço de fala é a Unificação de fala para texto, conversão de texto em fala e tradução de fala em uma única assinatura do Azure. Adicione fala a seus aplicativos, ferramentas e dispositivos com o SDK de fala, o SDK de dispositivos de fala ou as APIs REST.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/05/2019
ms.author: erhopf
ms.openlocfilehash: ee8b32634c92b873e82f540f04b53429de93d808
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611099"
---
# <a name="what-is-the-speech-service"></a>O que é o serviço de Voz?

O serviço de fala é a Unificação de fala para texto, conversão de texto em fala e tradução de fala em uma única assinatura do Azure. É fácil de fazer a fala habilitar seus aplicativos, ferramentas e dispositivos com o [SDK de fala](speech-sdk-reference.md), o [SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart)ou as [APIs REST](rest-apis.md).

> [!IMPORTANT]
> O serviço de fala substituiu API de Fala do Bing, Tradução de Fala e Fala Personalizada. Confira os guias de instruções _> migração_ para obter informações de migração.

Esses recursos compõem o serviço de fala. Use os links nesta tabela para saber mais sobre casos de uso comuns para cada recurso ou procure a referência de API.

| Serviço | Funcionalidade | Descrição | SDK | REST |
| ------- | ------- | ----------- | --- | ---- |
| [Conversão de fala em texto](speech-to-text.md) | Conversão de voz em texto | A fala a texto transcreve fluxos de áudio para texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use a conversão de fala em texto com [reconhecimento vocal (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar tentativas de usuário de fala transcrita e agir em comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
|         | [Transcrição do lote](batch-transcription.md) | A transcrição em lote permite a transcrição de fala em texto assíncrona de grandes volumes de dados. Esse é um serviço baseado em REST, que usa o mesmo ponto de extremidade como personalização e gerenciamento de modelos. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
|         | [Transcrição de conversa](conversation-transcription-service.md) | Habilita o reconhecimento de fala em tempo real, a identificação do orador e o diarization. É perfeito para transcrever reuniões presenciais com a capacidade de diferenciar os alto-falantes. | Sim | Não |
|         | [Criar modelos de Fala Personalizada](#customize-your-speech-experience) | Se estiver usando a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados para resolver o ruído de ambiente ou vocabulário específico do setor. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Conversão de texto em fala](text-to-speech.md) | Conversão de texto em voz | Conversão de texto em fala converte o texto de entrada em fala sintetizada por humanos usando a [linguagem de marcação de síntese de fala (SSML)](text-to-speech.md#core-features). Escolha entre vozes padrão e vozes neurais (consulte [suporte a idiomas](language-support.md)). | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
|         | [Criar vozes personalizadas](#customize-your-speech-experience) | Crie fontes de voz personalizadas exclusivas para sua marca ou produto. | Não | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução de Voz](speech-translation.md) | Tradução de voz | A conversão de fala permite tradução de fala em tempo real em vários idiomas para seus aplicativos, ferramentas e dispositivos. Use este serviço para tradução de fala a fala e conversão de fala em texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não |
| [Assistentes de voz](voice-assistants.md) | Assistentes de voz | Os assistentes de voz que usam o serviço de fala capacitam os desenvolvedores a criarem interfaces de conversação naturais e humanas para seus aplicativos e experiências. O serviço assistente de voz fornece uma interação rápida e confiável entre um dispositivo e uma implementação de assistente que usa o canal de fala de linha direta da estrutura de bot ou o serviço de comandos personalizados (versão prévia) integrado para a conclusão da tarefa. | [Sim](voice-assistants.md) | Não |

## <a name="news-and-updates"></a>Notícias e atualizações

Saiba o que há de novo no serviço de fala.

- Novembro de 2019
  - Foram adicionados dois novos estilos de fala, [`newscast`](speech-synthesis-markup.md#adjust-speaking-styles) e [`customerservice`](speech-synthesis-markup.md#adjust-speaking-styles) para a voz de `en-US-JessaNeural`.
- Setembro de 2019
  - Lançada a 1.7.0 do SDK de fala. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).
- Agosto de 2019
  - **Novo tutorial**: [Ativar voz para o bot com o SDK de C# fala,](tutorial-voice-enable-your-bot-speech-sdk.md)
  - Foi adicionado um novo estilo de fala, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles), para o `en-US-JessaNeural` Voice.
- Junho de 2019
  - Lançada a 1.6.0 do SDK de fala. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).
- 2019 de maio-a documentação agora está disponível para [transcrição de conversa](conversation-transcription-service.md), [transcrição do Call Center](call-center-transcription.md)e [assistentes de voz](voice-assistants.md).
- Maio de 2019
  - Lançada a 1.5.1 do SDK de fala. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).
  - Lançada o Speech SDK 1.5.0. Para obter uma lista completa de atualizações, aprimoramentos e problemas conhecidos, consulte [notas de versão](releasenotes.md).

## <a name="try-the-speech-service"></a>Experimente o serviço de fala

Oferecemos guias de início rápido nas linguagens de programação mais populares, cada uma projetada para que você execute códigos em menos de 10 minutos. Esta tabela contém os guias de início rápido mais populares para cada recurso. Use a navegação do lado esquerdo para explorar idiomas e plataformas adicionais.

| Conversão de fala em texto (SDK) | Conversão de texto em fala (SDK) | Tradução (SDK) |
| -------------------- | -------------------- | ----------------- |
| [Reconhecer a fala de um arquivo de áudio](quickstarts/speech-to-text-from-file.md) | [Sintetizar a fala em um arquivo de áudio](quickstarts/text-to-speech-audio-file.md) | [Traduzir fala para texto](quickstarts/translate-speech-to-text.md) |
| [Reconhecer a fala com um microfone](quickstarts/speech-to-text-from-microphone.md) | [Sintetizar a fala para um palestrante](quickstarts/text-to-speech.md) | [Traduzir a fala para vários idiomas de destino](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Reconhecer a fala armazenada no armazenamento de BLOBs](quickstarts/from-blob.md) | [Síntese assíncrona para áudio de formato longo](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Converter a fala em fala](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> A conversão de fala em texto e a conversão de texto em fala também têm pontos de extremidade REST e guias de início rápido associados.

Depois de ter tido a oportunidade de usar o serviço de fala, experimente nosso tutorial que ensina como reconhecer tentativas de fala usando o SDK de fala e o LUIS.

- [Tutorial: reconhecer tentativas de fala com o SDK de fala e o LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: Ativar voz para o bot com o SDK de fala,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutorial: criar um aplicativo Flask para traduzir texto, analisar sentimentos e sintetizar texto traduzido para fala, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obter o código de exemplo

O código de exemplo está disponível no GitHub para o serviço de fala. Esses exemplos abrangem cenários comuns, como a leitura de áudio de um arquivo ou fluxo, um reconhecimento contínuo e de captura única e o trabalho com modelos personalizados. Use estes links para exibir exemplos de SDK e REST:

- [Exemplos de conversão de fala em texto, texto em fala e tradução de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Exemplos do assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalizar sua experiência de fala

No entanto, o serviço de fala funciona bem com modelos internos, você talvez queira personalizar ainda mais e ajustar a experiência para seu produto ou ambiente. As opções de personalização variam desde o ajuste do modelo acústico até fontes de voz exclusivas para sua marca.

| Serviço de Voz | Plataforma | Descrição |
| -------------- | -------- | ----------- |
| Voz em Texto | [Conversão de voz personalizada](https://aka.ms/customspeech) | Personalize modelos de reconhecimento de fala para suas necessidades e dados disponíveis. Ultrapasse barreiras de reconhecimento de voz, como o estilo de fala, o vocabulário e o barulho de fundo. |
| Texto para Discurso | [Voz personalizada](https://aka.ms/customvoice) | Crie uma voz única reconhecível para as suas aplicações de Conversão de Texto em Voz com os dados de voz disponíveis. Você pode ajustar ainda mais as saídas de voz ajustando um conjunto de parâmetros de voz. |

## <a name="reference-docs"></a>Documentos de referência

- [SDK de Voz](speech-sdk-reference.md)
- [SDK de dispositivos de fala](speech-devices-sdk.md)
- [API REST: conversão de fala em texto](rest-speech-to-text.md)
- [API REST: conversão de texto em fala](rest-text-to-speech.md)
- [API REST: transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obtenha uma chave de assinatura do serviço de fala gratuitamente](get-started.md)
