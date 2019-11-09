---
title: Personalizar um modelo de linguagem no Video Indexer-Azure
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de linguagem no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b096b9352be65033f2fb782b118e815dc16b43b6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838322"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Personalizar um modelo de linguagem com Video Indexer

O Video Indexer dá suporte ao reconhecimento automático de fala por meio da integração com o [serviço de fala personalizada](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)da Microsoft. Você pode personalizar o modelo de linguagem carregando o texto de adaptação, ou seja, o texto do domínio cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que aparecem no texto de adaptação serão reconhecidas, assumindo a pronúncia padrão, e o modelo de linguagem aprenderá novas sequências prováveis de palavras. Os modelos de idioma personalizados têm suporte para inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo, Português do Brasil, híndi e coreano. 

Vamos pegar uma palavra altamente específica, como "kubernetes" (no contexto do serviço kubernetes do Azure), como um exemplo. Como a palavra é nova no Video Indexer, ela é reconhecida como "comunidades". Você precisa treinar o modelo para reconhecê-lo como "kubernetes". Em outros casos, as palavras existem, mas o modelo de linguagem não está esperando que elas apareçam em um determinado contexto. Por exemplo, "serviço de contêiner" não é uma sequência de duas palavras que um modelo de linguagem não especializado reconheceria como um conjunto específico de palavras.

Você tem a opção de carregar palavras sem contexto em uma lista em um arquivo de texto. Isso é considerado adaptação parcial. Como alternativa, você pode carregar arquivo (s) de texto de documentação ou sentenças relacionadas ao seu conteúdo para melhor adaptação.

Você pode usar as APIs de Video Indexer ou o site para criar e editar modelos de idioma personalizados, conforme descrito em tópicos na seção [próximas etapas](#next-steps) deste tópico.

## <a name="best-practices-for-custom-language-models"></a>Práticas recomendadas para modelos de linguagem personalizada

Video Indexer aprende com base nas probabilidades de combinações de palavras, para que você aprenda melhor:

* Forneça exemplos reais suficientes de frases, pois elas seriam faladas.
* Coloque apenas uma frase por linha, e não mais. Caso contrário, o sistema aprenderá probabilidades entre frases.
* Não há problema em colocar uma palavra como uma frase para impulsionar a palavra em relação a outras, mas o sistema aprende melhor com as frases completas.
* Ao introduzir novas palavras ou acrônimos, se possível, dê quantos exemplos de uso em uma frase completa para dar o máximo de contexto possível ao sistema.
* Tente colocar várias opções de adaptação e veja como elas funcionam para você.
* Evite a repetição exata da mesma frase várias vezes. Ele pode criar uma tendência em relação ao restante da entrada.
* Evite incluir símbolos incomuns (~, # @% &), pois eles serão descartados. As frases nas quais elas aparecem também serão descartadas.
* Evite colocar entradas muito grandes, como centenas de milhares de frases, pois isso causará diluir o efeito do aumento.

## <a name="next-steps"></a>Passos seguintes

[Personalizar o modelo de linguagem usando APIs](customize-language-model-with-api.md)

[Personalizar o modelo de idioma usando o site](customize-language-model-with-website.md)
