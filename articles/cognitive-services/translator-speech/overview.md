---
title: O que é o serviço Tradução de Voz?
titleSuffix: Azure Cognitive Services
description: Utilize a API do serviço Tradução de Voz para adicionar conversão de voz em voz e conversão de voz em texto das suas aplicações.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 24014bb06a779c214f18f966dfb1d26d61adee8d
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674859"
---
# <a name="what-is-translator-speech-api"></a>O que é a API de Voz do Microsoft Translator?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

A API de Voz do Microsoft Translator pode ser utilizada para adicionar traduções de ponto a ponto e em tempo real a aplicações, ferramentas ou qualquer solução que necessite de tradução de voz de vários idiomas independentemente do SO de destino ou de linguagens de programação. A API pode ser utilizada para a conversão de voz em voz e conversão de voz em texto.

A API de Texto do Microsoft Translator é um serviço do Azure, que faz parte da coleção de [APIs dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/) de aprendizagem automática e algoritmos de IA na cloud, prontamente consumível nos seus projetos de desenvolvimento.

Com a API de Voz do Microsoft Translator, as aplicações cliente transmitem áudio de voz ao serviço e recebem um fluxo de resultados baseados em texto e áudio, que incluem o texto reconhecido no idioma de origem e a respetiva tradução no idioma de destino. Os resultados são produzidos ao aplicar o Reconhecimento de Voz Automático (ASR) com tecnologia de redes neurais avançadas para o fluxo de áudio de entrada. A saída em bruto do ASR foi melhorada por uma nova técnica com o nome TrueText, para refletir melhor a intenção do utilizador. Por exemplo, o TrueText remove as disfluências (interjeições), palavras repetidas e restaura a pontuação e o uso de maiúsculas/minúsculas adequados. A capacidade de mascarar ou excluir linguagem obscena também está incluída. Os mecanismos de reconhecimento e tradução foram preparados especificamente para processar vozes em conversa.

O serviço Tradução de Voz utiliza a deteção de silêncio para determinar o fim de uma expressão oral. Após uma pausa na atividade de voz, o serviço irá transmitir o resultado final da expressão oral. O serviço também pode enviar resultados parciais, que proporcionam reconhecimentos e traduções intermediários para uma expressão em curso.

Na conversão de voz em voz, o serviço proporciona a capacidade de sintetizar a voz (texto para voz) do texto falado nos idiomas de destino. O áudio de texto em voz é criado no formato especificado pelo cliente. Os formatos WAV e MP3 estão disponíveis.

A API de Voz do Microsoft Translator utiliza o protocolo WebSocket para fornecer um canal de comunicação duplo e complexo entre o cliente e o servidor.

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator
O Microsoft Translator é um serviço de tradução automática com base na cloud. No núcleo deste serviço estão a [API de Texto do Microsoft Translator](https://www.microsoft.com/en-us/translator/translatorapi.aspx) e a API de Voz do Microsoft Translator que potencializam vários produtos e serviços da Microsoft e são utilizados por milhares de empresas em todo o mundo nas suas aplicações e fluxos de trabalho, permitindo que o conteúdo alcance o público no mundo inteiro.

Saiba mais sobre o [serviço do Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Tradução Automática Neural do Microsoft Translator (NMT)
A API de Voz do Microsoft Translator utiliza a tradução automática de estatísticas (SMT) legada e a tradução automática neural (NMT) mais recente para fornecer as traduções.

A tradução automática de estatísticas atingiu um patamar em termos de melhoria do desempenho. A qualidade da tradução já não está a melhorar de forma significativa nos sistemas genéricos com SMT. Uma nova tecnologia de tradução baseada em IA está a ganhar força com base em Redes Neurais (NN).

A NMT oferece melhores traduções, não apenas do ponto de vista da qualidade de tradução em bruto, mas também porque soam mais fluentes e humanas do que as da SMT.
O motivo principal desta fluidez deve-se à NMT utilizar o contexto completo de uma frase para traduzir palavras. A SMT utiliza apenas o contexto imediato de algumas palavras antes e depois de cada palavra.

Os modelos da NMT são o núcleo da API e não estão visíveis para os utilizadores finais. As únicas diferenças significativas são:
* A melhoria da qualidade da tradução, especialmente em idiomas como chinês, japonês e árabe
* A incompatibilidade com as funcionalidades existentes de personalização do Hub (para utilização com a API de Texto do Microsoft Translator)

Todos os idiomas de tradução de voz suportados têm a tecnologia NMT. Por conseguinte, toda a conversão de voz em voz utiliza NMT.

A conversão de voz em texto pode utilizar uma combinação de NMT e SMT, consoante o par de idiomas. Se o idioma de destino for suportado por NMT, a tradução inteira terá a tecnologia NMT. Se o idioma de destino não for suportado por NMT, a tradução é uma mistura de NMT e SMT, com o inglês como a "referência" entre os dois idiomas.

Veja os idiomas suportados em [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx).

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Inscreva-se](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Começar a programar](quickstarts/csharp.md)

## <a name="see-also"></a>Consulte também
- [Página de Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/)
- [Página de Produto dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)
- [Informações sobre soluções e preços](https://www.microsoft.com/en-us/translator/home.aspx)
