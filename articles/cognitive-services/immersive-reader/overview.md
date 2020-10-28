---
title: O que é o Leitor Imersivo?
titleSuffix: Azure Cognitive Services
description: O Immersive Reader é uma ferramenta que é projetada para ajudar pessoas com diferenças de aprendizagem ou ajudar novos leitores e aprendizes de linguagem com a compreensão da leitura.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: leitores, aprendizes de línguas, exibir imagens, melhorar a leitura, ler conteúdos, traduzir
ms.openlocfilehash: 0661bd5f917a2bf071f11393ad8ac9af53ddfe97
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631244"
---
# <a name="what-is-immersive-reader"></a>O que é a Leitura Avançada?

[O Immersive Reader](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente concebida que implementa técnicas comprovadas para melhorar a compreensão da leitura para novos leitores, aprendizes de línguas e pessoas com diferenças de aprendizagem como a dislexia. Com a biblioteca de clientes Immersive Reader, pode aproveitar a mesma tecnologia utilizada no Microsoft Word e microsoft One Note para melhorar as suas aplicações web. 

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>Use o Leitor Imersivo para melhorar a acessibilidade à leitura 

O Leitor Imersivo foi concebido para tornar a leitura mais fácil e acessível para todos. Vamos dar uma olhada em algumas das principais características do Leitor Imersivo.

### <a name="isolate-content-for-improved-readability"></a>Isolar o conteúdo para uma melhor prontidão

O Leitor Imersivo isola o conteúdo para melhorar a legibilidade. 

  ![Isolar conteúdo para uma melhor legibilidade com leitor imersivo](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Exibir imagens para palavras comuns

Para termos comumente utilizados, o Leitor Imersivo apresentará uma imagem.

  ![Dicionário de imagem com leitor imersivo](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>Destacar partes da fala

O Leitor Imersivo pode ser usado para ajudar os alunos a entender partes da fala e gramática, destacando verbos, substantivos, pronomes e muito mais.

  ![Mostrar partes da fala com leitor imersivo](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Leia o conteúdo em voz alta

A síntese de fala (ou texto-a-voz) é cozida no serviço Leitor Imersivo, que permite aos seus leitores selecionar texto para ser lido em voz alta. 

  ![Leia o texto em voz alta com o Leitor Imersivo](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Traduzir conteúdo em tempo real

O Leitor Imersivo pode traduzir texto em muitas línguas em tempo real. Isto é útil para melhorar a compreensão para os leitores aprenderem uma nova linguagem.

  ![Traduzir texto com leitor imersivo](./media/translation.png)

### <a name="split-words-into-syllables"></a>Dividir palavras em sílabas

Com o Leitor Imersivo pode quebrar palavras em sílabas para melhorar a legibilidade ou para soar novas palavras.

  ![Quebre palavras em sílabas com leitor imersivo](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Como funciona o Leitor Imersivo?

O Immersive Reader é uma aplicação web autónoma. Quando invocado através da biblioteca de clientes Immersive Reader é apresentado em cima da sua aplicação web existente num `iframe` . Quando a sua aplicação wep chama o serviço Leitor Imersivo, especifique o conteúdo para mostrar o leitor. A biblioteca de clientes Immersive Reader lida com a criação e estilo do `iframe` e da comunicação com o serviço de backend Do Leitor Imersivo. O serviço Immersive Reader processa o conteúdo para partes da fala, texto para a fala, tradução e muito mais.

## <a name="get-started-with-immersive-reader"></a>Começa com o Leitor Imersivo

A biblioteca de clientes Immersive Reader está disponível em C#, JavaScript, Java (Android), Kotlin (Android) e Swift (iOS). Introdução a:

* [Quickstart: Use a biblioteca de clientes imersivos Reader](quickstarts/client-libraries.md)

## <a name="next-steps"></a>Passos seguintes

Introdução à Leitura Avançada:

* Leia a biblioteca de [clientes imersivo Reader Referência](./reference.md)
* Explore a [biblioteca de clientes imersivos reader no GitHub](https://github.com/microsoft/immersive-reader-sdk)
