---
title: Voz com serviços de voz do Azure
titleSuffix: Azure Cognitive Services
description: Voz, conversão de voz dos serviços do Azure é um serviço que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala sintetizada de semelhante à humana natural. Escolha entre padrão e neurais vozes ou criar a sua voz personalizada exclusivo para o produto ou marca. 75 + vozes standard estão disponíveis em mais de 45 idiomas e localidades e 5 vozes neurais estão disponíveis em 4 idiomas e localidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: c5f7b0b739e9311e2527e63db7279d2075f0f1ff
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341273"
---
# <a name="what-is-text-to-speech"></a>O que é o texto para discurso?

Voz, conversão de voz dos serviços do Azure é um serviço que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala sintetizada de semelhante à humana natural. Escolha entre padrão e neurais vozes ou criar a sua voz personalizada exclusivo para o produto ou marca. 75 + vozes standard estão disponíveis em mais de 45 idiomas e localidades e 5 vozes neurais estão disponíveis em 4 idiomas e localidades. Para obter uma lista completa, consulte [idiomas suportados](language-support.md#text-to-speech).

Tecnologia de texto para discurso permite que os criadores de conteúdo interagir com os seus utilizadores de formas diferentes. Voz pode melhorar a acessibilidade, fornecendo os utilizadores com uma opção para interagir com o conteúdo poder ouvi-lo. Se o usuário tem um visual impairment, uma deficiência de aprendizagem, ou necessita de informações de navegação, obtendo, voz pode melhorar uma experiência existente. Voz também é um complemento valioso para bots de voz e assistentes virtual.


Ao tirar partido do Speech Synthesis Markup Language (SSML), uma linguagem de marcação baseada em XML, os desenvolvedores que usam o serviço de texto para voz podem especificar como entrado de texto é convertido em fala sintetizada. Com SSML, pode ajustar o argumento de venda, pronúncia, falando taxa, volume e muito mais. Para obter mais informações, consulte [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Vozes padrão

Vozes padrão são criadas usando técnicas de estatísticas de síntese Paramétrica e/ou de síntese de concatenação. Estes vozes são altamente inteligível e som natural. É possível habilitar facilmente seus aplicativos falar em mais de 45 idiomas, com uma vasta gama de opções de voz. Estes vozes fornecem precisão pronúncia elevada, incluindo suporte para abreviaturas, expansões de acrônimos, interpretações de data/hora, polyphones e muito mais. Utilize a voz como método padrão para melhorar a acessibilidade de seus aplicativos e serviços, permitindo que os utilizadores interajam com o seu conteúdo poder ouvi-lo.

### <a name="neural-voices"></a>Vozes neurais

Vozes neurais usam redes neurais profundas para superar os limites dos sistemas de texto para voz tradicionais em correspondentes aos padrões de estresse e intonation num idioma falado e resumir as unidades de conversão de voz numa voz de computador. Voz Standard divide prosody em separado de análise linguística e passos de predição acústico que são regidos pelos modelos independentes, que podem resultar em síntese de voz muffled. Nossa capacidade neural faz síntese de voz e de predição de prosody simultaneamente, que resulta numa voz mais fluida e com o som natural.

Vozes neurais podem ser utilizados para tornar as interações com chatbots e assistentes virtual mais natural e envolventes converter textos digital, como o e-livros em audiobooks e melhorar a sistemas de navegação no carro. Com o prosody natural de semelhante à humana e articulation clara de palavras, o vozes neurais reduzem significativamente fadiga escuta quando interage com os sistemas de IA.

Vozes neurais oferecem suporte a estilos diferentes, tais como neutra e cheerful. Por exemplo, a voz Jessa (en-US) compatível com o protocolo cheerfully, que está otimizado para conversação quente, feliz. Pode ajustar a saída de voz, como o tom, o argumento de venda e acelerar usando [Speech Synthesis Markup Language](speech-synthesis-markup.md). Para obter uma lista completa de vozes disponíveis, consulte [idiomas suportados](language-support.md#text-to-speech).

Para saber mais sobre os benefícios de vozes neurais, veja [serviço de texto para discurso neural novo da Microsoft ajuda a máquinas falar, como as pessoas](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Vozes personalizadas

Personalização de voz permite-lhe criar uma voz reconhecível, um do-única para sua marca. Para criar o seu tipo de voz personalizada, fazer uma gravação de studio e carregar os scripts associados como os dados de treinamento. O serviço, em seguida, cria um modelo de voz única ajustado para a gravação. Pode utilizar este tipo de voz personalizada para sintetizar voz. Para obter mais informações, consulte [vozes personalizadas](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) é uma linguagem de marcação baseada em XML que permite aos programadores especificar como entrado de texto é convertida em fala sintetizada com o serviço de texto para voz. Em comparação com texto sem formatação, SSML permite aos programadores ajustar o argumento de venda, pronúncia, fala taxa, o volume e mais a saída de texto para voz. Pontuação normal, como colocar em pausa após um período ou usando o intonation correto quando uma sentença termina com um ponto de interrogação são manipulados automaticamente.

Todas as entradas de texto enviadas para o serviço de texto para voz tem de ser estruturadas como SSML. Para obter mais informações, consulte [Speech Synthesis Markup Language](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Nota de preços

Ao utilizar o serviço de texto para voz, é-lhe cobrada para cada caractere que é convertido para conversão de voz, incluindo a pontuação. Enquanto o próprio documento SSML não está sujeitos a faturação, elementos opcionais que são utilizados para ajustar a forma como o texto é convertido para conversão de voz, como fonemas e pitch, são contabilizados como caracteres faturáveis. Aqui está uma lista do que é a cobrar:

* Texto passado para o serviço de texto para discurso no corpo do pedido SSML
* Todas as marcações dentro do campo de texto do corpo do pedido no formato SSML, exceto para `<speak>` e `<voice>` etiquetas
* Letras, pontuação, espaços, tabulações, marcação e todos os caracteres de espaço em branco
* Cada ponto de código definido em Unicode

Para obter informações detalhadas, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Cada caractere de idioma chinês, japonês e coreano é contabilizado como dois caracteres para faturação.

## <a name="core-features"></a>Principais recursos

Esta tabela lista os principais recursos de voz:

| Caso de utilização | SDK | REST |
|----------|-----|------|
| Converta texto em voz. | Sim | Sim |
| Carregar conjuntos de dados para adaptação de voz. | Não | Sim\* |
| Criar e gerir modelos de tipo de letra de voz. | Não | Sim\* |
| Criar e gerir implementações de tipo de letra de voz. | Não | Sim\* |
| Criar e gerir os testes de tipo de letra de voz. | Não | Sim\* |
| Gerir subscrições. | Não | Sim\* |

\* *Esses serviços estão disponíveis com o ponto de extremidade cris.ai. Ver [Swagger referência](https://westus.cris.ai/swagger/ui/index). Estes formação de voz personalizada e APIs de gestão de implementam a limitação de que os pedidos de limites para 25 por 5 segundos, enquanto a síntese de fala API propriamente dita implementa limitação que permite 200 pedidos por segundo, como a mais alta. Quando ocorre a limitação, será notificado através de cabeçalhos de mensagens.*

## <a name="get-started-with-text-to-speech"></a>Introdução ao texto em voz

Oferecemos inícios rápidos, criados para executar o código em menos de 10 minutos. Esta tabela inclui uma lista de guias de introdução de texto para discurso organizados por idioma.

### <a name="sdk-quickstarts"></a>Inícios rápidos do SDK

| Início rápido (SDK) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Procurar](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Inícios rápidos REST

| Início rápido (REST) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Janela, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Janela, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Código de exemplo

Código de exemplo para voz está disponível no GitHub. Esses exemplos abrangem a conversão de texto para discurso em linguagens de programação mais populares.

* [Exemplos de texto para voz (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de texto para voz (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Dispositivos de voz SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

* [Obter uma subscrição de serviços de voz gratuito](get-started.md)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
