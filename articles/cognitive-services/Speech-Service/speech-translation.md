---
title: Tradução de voz com serviços de voz do Azure
titlesuffix: Azure Cognitive Services
description: Os serviços de voz permitem que adicione-a-ponto, em tempo real, vários idioma tradução de voz às suas aplicações, ferramentas e dispositivos. A mesma API pode ser utilizada para a tradução de voz para voz e conversão de voz em texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 428dba9e8ff5ec072d5172fee357fd5319d04ad8
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657735"
---
# <a name="what-is-speech-translation"></a>O que é a tradução de voz?

Tradução de voz, conversão de voz dos serviços do Azure, permite a tradução de voz para voz e conversão de voz em texto em tempo real, vários idioma de fluxos de áudio. Com o SDK de voz, seus aplicativos, ferramentas e dispositivos têm acesso ao transcrições de origem e saídas de tradução de áudio fornecido. Resultados de transcrição e de tradução provisórias são devolvidos como voz é detectada e resultados finais podem ser convertidos em fala sintetizada.

Mecanismo de tradução da Microsoft utiliza a tecnologia de duas abordagens diferentes: estatísticos de tradução automática (SMT) e a tradução automática neuronal (NMT). SMT utiliza análise avançada de estatística para estimar as traduções de possíveis recomendadas tendo em conta o contexto de algumas palavras. Com NMT, redes neurais são usadas para fornecer mais precisas, com o som natural traduções, usando o contexto completo de frases para traduzir palavras.

Hoje em dia, a Microsoft utiliza NMT para a tradução para linguagens mais populares. Todos os [idiomas disponíveis para a tradução de voz para voz](language-support.md#speech-translation) têm a tecnologia NMT. Tradução de voz em texto utilizar SMT ou NMT consoante o par de idioma. Quando o idioma de destino for suportado pela NMT, a tradução inteira é com a tecnologia de NMT. Quando o idioma de destino não é suportado pela NMT, a tradução é uma mistura de NMT e SMT, usando o inglês como uma tabela "dinâmica" entre as duas linguagens.

## <a name="core-features"></a>Principais recursos

Seguem-se as funcionalidades disponíveis através do SDK de voz e REST APIs:

| Caso de utilização | SDK | REST |
|----------|-----|------|
| Tradução de voz em texto com resultados de reconhecimento. | Sim | Não |
| Tradução de voz para voz. | Sim | Não |
| Resultados de reconhecimento e a tradução intermediárias. | Sim | Não |

## <a name="get-started-with-speech-translation"></a>Introdução à tradução de voz

Oferecemos inícios rápidos, criados para executar o código em menos de 10 minutos. Esta tabela inclui uma lista dos inícios rápidos de tradução de voz organizados por idioma.

| Início Rápido | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows, Linux, macOS | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

Código de exemplo para o SDK de voz está disponível no GitHub. Esses exemplos abrangem os cenários comuns, como ler o áudio de um arquivo ou fluxo, contínua e única reconhecimento/tradução e trabalhar com modelos personalizados.

* [Exemplos de voz em texto e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Se seus aplicativos, ferramentas ou produtos estão a utilizar o [a API de voz do Translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), nós criamos guias para ajudar a migrar para os serviços de voz.

* [Migrar a partir da API de voz do Translator para os serviços de voz](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Dispositivos de voz SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
* [Obtenha o SDK de voz](speech-sdk.md)
