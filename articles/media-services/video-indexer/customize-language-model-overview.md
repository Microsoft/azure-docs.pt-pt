---
title: Personalize um modelo de idioma em Indexer de Vídeo - Azure
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral do que é um modelo de Idioma no Indexer de Vídeo e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: d264b0d35be5114d35713f793b771e42449c9230
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745676"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Personalize um modelo de idioma com o Indexante de Vídeo

O Indexer de vídeo suporta o reconhecimento automático da fala através da integração com o Serviço de [Discurso Personalizado](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)da Microsoft . Pode personalizar o modelo Idioma carregando texto de adaptação, nomeadamente texto do domínio cujo vocabulário gostaria que o motor se adaptasse. Assim que treinar o seu modelo, novas palavras que aparecem no texto de adaptação serão reconhecidas, assumindo a pronúncia padrão, e o modelo Linguístico aprenderá novas sequências prováveis de palavras. Os modelos de linguagem personalizada são suportados para inglês, espanhol, francês, alemão, italiano, chinês (simplificado), japonês, russo, português, hindi e coreano. 

Vamos pegar numa palavra que é altamente específica, como "Kubernetes" (no contexto do serviço Azure Kubernetes), como exemplo. Uma vez que a palavra é nova para o Indexer de Vídeo, é reconhecida como "comunidades". É preciso treinar o modelo para o reconhecer como "Kubernetes". Noutros casos, as palavras existem, mas o modelo Linguístico não espera que apareçam num determinado contexto. Por exemplo, o "serviço de contentores" não é uma sequência de duas palavras que um modelo de Linguagem não especializado reconheceria como um conjunto específico de palavras.

Tem a opção de carregar palavras sem contexto numa lista num ficheiro de texto. Isto é considerado uma adaptação parcial. Em alternativa, pode fazer o upload de ficheiros de texto ou frases relacionadas com o seu conteúdo para uma melhor adaptação.

Pode utilizar as APIs do Indexer de Vídeo ou o website para criar e editar modelos de Idioma personalizados, conforme descrito em tópicos na secção [de passos Seguintes](#next-steps) deste tópico.

## <a name="best-practices-for-custom-language-models"></a>Boas práticas para modelos linguísticos personalizados

O Indexer de vídeo aprende com base nas probabilidades de combinações de palavras, para aprender melhor:

* Dêem exemplos suficientes de frases como seriam ditas.
* Colocar apenas uma frase por linha, não mais. Caso contrário, o sistema aprenderá probabilidades através das frases.
* Não faz mal colocar uma palavra como frase para aumentar a palavra contra os outros, mas o sistema aprende melhor com frases completas.
* Ao introduzir novas palavras ou siglas, se possível, dê o maior número possível de exemplos de utilização numa frase completa para dar o máximo de contexto possível ao sistema.
* Tente colocar várias opções de adaptação, e veja como funcionam para si.
* Evite repetir a mesma frase várias vezes. Pode criar preconceito contra o resto da entrada.
* Evite incluir símbolos incomuns (~, @ % &) pois serão descartados. As frases em que aparecem também serão descartadas.
* Evite colocar inputs demasiado grandes, como centenas de milhares de frases, porque fazê-lo irá diluir o efeito do aumento.

## <a name="next-steps"></a>Passos seguintes

[Personalizar modelo de idioma usando APIs](customize-language-model-with-api.md)

[Personalize o modelo de idioma usando o site](customize-language-model-with-website.md)
