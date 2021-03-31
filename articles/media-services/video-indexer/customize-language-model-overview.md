---
title: Personalize um modelo de linguagem no Indexador de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do que é um modelo de Linguagem no Indexante de Vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87047135"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Personalize um modelo de linguagem com indexador de vídeo

O Video Indexer suporta o reconhecimento automático da fala através da integração com o [Microsoft Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Pode personalizar o modelo Language carregando texto de adaptação, nomeadamente texto do domínio cujo vocabulário gostaria que o motor se adaptasse. Assim que treinar o seu modelo, novas palavras que aparecem no texto de adaptação serão reconhecidas, assumindo a pronúncia padrão, e o modelo de Linguagem aprenderá novas sequências prováveis de palavras. Os modelos de língua personalizada são suportados para inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo, português, hindi e coreano. 

Vamos pegar numa palavra altamente específica, como "Kubernetes" (no contexto do serviço Azure Kubernetes), como exemplo. Uma vez que a palavra é nova no Video Indexer, é reconhecida como "comunidades". É preciso treinar o modelo para o reconhecer como "Kubernetes". Noutros casos, as palavras existem, mas o modelo linguístico não espera que apareçam num determinado contexto. Por exemplo, "serviço de contentores" não é uma sequência de duas palavras que um modelo de linguagem não especializado reconheceria como um conjunto específico de palavras.

Tem a opção de carregar palavras sem contexto numa lista num ficheiro de texto. Isto é considerado adaptação parcial. Em alternativa, pode fazer o upload de ficheiros de texto de documentação ou frases relacionadas com o seu conteúdo para uma melhor adaptação.

Pode utilizar as APIs do Indexador de Vídeo ou o website para criar e editar modelos de Linguagem personalizados, conforme descrito em tópicos na secção [etapas seguintes](#next-steps) deste tópico.

## <a name="best-practices-for-custom-language-models"></a>Melhores práticas para modelos de linguagem personalizada

O Video Indexer aprende com base em probabilidades de combinações de palavras, para aprender melhor:

* Dêem exemplos reais suficientes de frases como seriam ditas.
* Ponha apenas uma frase por linha, não mais. Caso contrário, o sistema aprenderá probabilidades através de frases.
* Não há problema em colocar uma palavra como frase para aumentar a palavra contra os outros, mas o sistema aprende melhor com frases completas.
* Ao introduzir novas palavras ou siglas, se possível, dê o maior número de exemplos de uso numa frase completa para dar o máximo de contexto possível ao sistema.
* Tente colocar várias opções de adaptação, e veja como funcionam para si.
* Evite a repetição da mesma frase várias vezes. Pode criar preconceitos contra o resto da entrada.
* Evite incluir símbolos incomuns (~, # % &) pois serão descartados. As frases em que aparecem também serão descartadas.
* Evite colocar entradas demasiado grandes, como centenas de milhares de frases, porque fazê-lo irá diluir o efeito do aumento.

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo de linguagem usando APIs](customize-language-model-with-api.md)

[Personalizar modelo de linguagem usando o site](customize-language-model-with-website.md)
