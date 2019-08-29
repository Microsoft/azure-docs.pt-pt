---
title: Speech-to-Text-serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso de conversão de fala em texto no serviço de fala, também conhecido como conversão de fala em texto, permite a transcrição em tempo real de fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e agir como entrada de comando. Esse serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft usa para os produtos da Cortana e do Office e trabalha diretamente com a tradução e conversão de texto em fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fa80ebea93ae897ba01ff54bdb797ed4194665cd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068868"
---
# <a name="what-is-speech-to-text"></a>O que é conversão de fala em texto?

A conversão de fala em texto dos serviços de fala do Azure, também conhecida como conversão de fala em texto, permite a transcrição em tempo real de fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e agir como entrada de comando. Esse serviço é alimentado pela mesma tecnologia de reconhecimento que a Microsoft usa para os produtos da Cortana e do Office e trabalha diretamente com a tradução e conversão de texto em fala.  Para obter uma lista completa de idiomas de fala para texto disponíveis, consulte [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Por padrão, o serviço de fala a texto usa o modelo de linguagem universal. Esse modelo foi treinado usando dados de propriedade da Microsoft e é implantado na nuvem. É ideal para cenários de conversação e de ditado. Se estiver usando a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados para resolver o ruído de ambiente ou vocabulário específico do setor.

Você pode facilmente capturar áudio de um microfone, ler de um fluxo ou acessar arquivos de áudio do armazenamento com o SDK de fala e as APIs REST. O SDK de fala dá suporte a WAV/PCM de 16 bits, 16 kHz/8 kHz, áudio de canal único para reconhecimento de fala. Formatos de áudio adicionais têm suporte usando o [ponto de extremidade REST de fala para texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou o [serviço de transcrição do lote](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Principais recursos

Aqui estão os recursos disponíveis por meio do SDK de fala e APIs REST:

| Caso de utilização | SDK | REST |
|----------|-----|------|
| Transcrever declarações curtos (< 15 segundos). Dá suporte apenas ao resultado final da transcrição. | Sim | Sim |
| Transcrição contínua de longa declarações e áudio de streaming (> 15 segundos). Dá suporte a resultados provisórios e finais de transcrição. | Sim | Não |
| Derive tentativas de resultados de reconhecimento com [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Sim | Não\* |
| Transcrição de lote de arquivos de áudio de forma assíncrona. | Não | Ok\** |
| Crie e gerencie modelos de fala. | Não | Ok\** |
| Criar e gerenciar implantações de modelo personalizado. | Não | Ok\** |
| Crie testes de precisão para medir a precisão do modelo de linha de base versus modelos personalizados. | Não | Ok\** |
| Gerenciar assinaturas. | Não | Ok\** |

\* *LUIS intenções e entidades podem ser obtidas através de uma subscrição separada do LUIS. Com essa assinatura, o SDK pode chamar LUIS para você e fornecer resultados de entidade e de intenção. Com a API REST, pode chamar o LUIS para derivar intenções e entidades com a sua subscrição do LUIS.*

\** *Esses serviços estão disponíveis usando o ponto de extremidade cris.ai. Consulte [referência do Swagger](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Introdução à conversão de fala em texto

Oferecemos guias de início rápido nas linguagens de programação mais populares, cada uma projetada para que você execute códigos em menos de 10 minutos. [Esta tabela](https://aka.ms/csspeech#5-minute-quickstarts) inclui uma lista completa de guias de início rápido do SDK de fala organizados por platfrom e linguagem.  A referência de API também pode ser encontrada [aqui](https://aka.ms/csspeech#reference).

Se você preferir usar o serviço REST de fala em texto, consulte [APIs REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de exemplo

Depois de ter tido a oportunidade de usar os serviços de fala, experimente nosso tutorial que ensina como reconhecer tentativas de fala usando o SDK de fala e o LUIS.

* [Tutorial: Reconheça as intenções da fala com o SDK de fala e o LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

O código de exemplo para o SDK de fala está disponível no GitHub. Esses exemplos abrangem cenários comuns, como a leitura de áudio de um arquivo ou fluxo, um reconhecimento contínuo e de captura única e o trabalho com modelos personalizados.

* [Exemplos de conversão de fala em texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Amostras de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personalização

Além do modelo de linha de base padrão usado pelos serviços de fala, você pode personalizar modelos para suas necessidades com os dados disponíveis, para superar as barreiras de reconhecimento de fala, como estilo de fala, vocabulário e ruído de fundo, consulte [fala personalizada](how-to-custom-speech.md)

> [!NOTE]
> As opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](supported-languages.md)).

## <a name="migration-guides"></a>Guias de migração

> [!WARNING]
> Fala do Bing será encerrado em 15 de outubro de 2019.

Se seus aplicativos, ferramentas ou produtos estiverem usando o Fala do Bing APIs ou Fala Personalizada, criamos guias para ajudá-lo a migrar para os serviços de fala.

* [Migrar do Fala do Bing para os serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrar do Fala Personalizada para os serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](https://aka.ms/csspeech)
* [SDK de dispositivos de fala](speech-devices-sdk.md)
* [API REST: Conversão de fala em texto](rest-speech-to-text.md)
* [API REST: Conversão de texto em fala](rest-text-to-speech.md)
* [API REST: Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

* [Obtenha uma chave de assinatura de serviços de fala gratuitamente](get-started.md)
* [Obtenha o SDK de fala](speech-sdk.md)
