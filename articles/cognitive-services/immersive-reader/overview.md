---
title: O que é a API de leitura de imersão?
titleSuffix: Azure Cognitive Services
description: A API de leitura de imersão é uma ferramenta que pode ser usada para acomodar pessoas com diferenças de aprendizado ou ajudar novos leitores e aprendizes de linguagem.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 3e6889cd5d26b88cedd6b110510cb89777252f54
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904372"
---
# <a name="what-is-immersive-reader"></a>O que é a Leitura Avançada?

O [leitor de imersão](https://www.onenote.com/learningtools) é uma ferramenta projetada de forma inclusiva que implementa técnicas comprovadas para melhorar a compreensão da leitura para leitores emergentes, aprendizes de linguagem e pessoas com diferenças de aprendizagem, como dislexia.

Pode utilizar a Leitura Avançada na sua aplicação Web com o SDK da Leitura Avançada.

## <a name="what-does-immersive-reader-do"></a>O que o leitor de imersão faz?

O leitor de imersão foi projetado para tornar a leitura mais acessível para todos.

* Mostra o conteúdo em uma exibição de leitura mínima

  ![Leitura Avançada](./media/immersive-reader.png)

* Exibe imagens de palavras usadas com frequência

  ![Dicionário de imagens](./media/picture-dictionary.png)

* Realça substantivos, verbos, adjetivos e advérbios

  ![Partes da fala](./media/parts-of-speech.png)

* Leia seu conteúdo de maneira muito alta para você

  ![Lido em voz alta](./media/read-aloud.png)

* Traduz o conteúdo em outro idioma

  ![Tradução](./media/translation.png)

* Quebra as palavras em sílabas

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Como funciona o leitor de imersão?

O leitor de imersão é um aplicativo Web autônomo que, quando invocado usando o SDK de JavaScript do leitor de imersão, é exibido na parte superior do aplicativo Web existente por meio de um `iframe`. Ao chamar a API para iniciar o leitor de imersão, você especifica o conteúdo que deseja mostrar no leitor de imersão. Nosso SDK trata da criação e do estilo do `iframe` e da comunicação com o serviço de back-end do leitor de imersão, que processa o conteúdo de partes de fala, conversão de texto em fala, tradução e assim por diante.

## <a name="next-steps"></a>Passos seguintes

Introdução à Leitura Avançada:

* Ir para o [início rápido](./quickstart.md)
* Explore o [SDK do leitor de imersão no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Ler a [referência do SDK do leitor de imersão](./reference.md)