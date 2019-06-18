---
title: Personalizar um modelo de idioma no indexador de vídeo - Azure
titlesuffix: Azure Media Services
description: Este artigo fornece uma descrição geral sobre o que é um modelo de idioma no indexador de vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 516ecd8842e7b673201cc640b283c081a02d2b2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799547"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Personalizar um modelo de idioma com o indexador de vídeo

Reconhecimento de voz automático por meio da integração com a Microsoft suporta o indexador de vídeos [serviço de voz personalizada](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Pode personalizar o modelo de idioma através do carregamento de texto de adaptação, ou seja, o texto do domínio cujo vocabulário que pretende que o mecanismo para se adaptar às. Depois de preparar o seu modelo, serão reconhecidas novas palavras que aparecem no texto adaptação, partindo do princípio de pronúncia de padrão, e o modelo de idioma irá aprender novas seqüências prováveis de palavras. Modelos de linguagem personalizados são suportados para inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo, português (Brasil), Híndi e coreano. 

Vamos dar uma palavra que é altamente específica, como o "Kubernetes" (no contexto do serviço Kubernetes do Azure), por exemplo. Como o word é novo ao Video Indexer, ela é reconhecida como "Comunidades". Terá de preparar o modelo reconhecê-lo como "Kubernetes". Em outros casos, as palavras existem, mas o modelo de idioma não está esperando serem apresentadas num determinado contexto. Por exemplo, "serviço de contentor" não é uma sequência de 2 palavras que um modelo de idioma não especializado reconheceria como um conjunto específico de palavras.

Tem a opção de carregar palavras sem contexto numa lista num ficheiro de texto. Esse é considerado adaptação parcial. Em alternativa, pode carregar ficheiros de texto de documentação ou frases relacionados com o seu conteúdo para adaptação de melhor.

Pode utilizar as APIs do indexador de vídeo ou o Web site para criar e editar modelos de idioma personalizados, conforme descrito nos tópicos de [próximos passos](#next-steps) secção deste tópico.

## <a name="best-practices-for-custom-language-models"></a>Melhores práticas para modelos de idioma personalizados

O Video Indexer aprende com base no probabilidades de combinações word, por isso, para saber mais:

* Dê suficiente exemplos reais de frases conforme eles seriam ser ditas.
* Coloque uma frase apenas um por linha, não mais. Caso contrário, o sistema irá aprender probabilidades entre frases.
* Há problema colocar uma palavra como uma frase para aumentar a palavra em relação a outras pessoas, mas o sistema aprende o melhor de frases completas.
* Ao introduzir novas palavras ou acrônimos, se possível, dar a tantos exemplos de utilização numa frase completa para dar o máximo de contexto possível no sistema.
* Tente colocar várias opções de adaptação e veja como funcionam para.
* Evite a repetição de mesma frase exata várias vezes. Poderá criar psicológico contra o resto da entrada.
* Evitar a inclusão de símbolos incomuns (~, # % &) à medida que irão obter descartados. Também irão obter eliminadas as frases em que aparecem.
* Evite colocar entradas demasiado grandes, por exemplo, a centenas de milhares de frases, porque ao fazê-lo será diluir o efeito de adaptativo.

## <a name="next-steps"></a>Passos Seguintes

[Personalizar o modelo de idioma com APIs](customize-language-model-with-api.md)

[Personalizar o modelo de idioma com o Web site](customize-language-model-with-website.md)
