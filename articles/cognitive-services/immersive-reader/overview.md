---
title: O que é a API de leitura de imersão?
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a API do leitor de imersão.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f84804fbad07357706e786fc449fce9e42a5ec4a
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688336"
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

O leitor de imersão é um aplicativo Web autônomo que, quando invocado usando o SDK de JavaScript do leitor de imersão, é exibido na parte superior do `iframe`aplicativo Web existente por meio de um. Ao chamar a API para iniciar o leitor de imersão, você especifica o conteúdo que deseja mostrar no leitor de imersão. Nosso SDK trata da criação e do estilo do `iframe` e da comunicação com o serviço de back-end do leitor de imersão, que processa o conteúdo de partes de fala, texto em fala, conversão e assim por diante.

## <a name="next-steps"></a>Passos seguintes

Introdução à Leitura Avançada:

* Ir para o [início rápido](./quickstart.md)
* Explore o [SDK do leitor de imersão no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Ler a [referência do SDK do leitor de imersão](./reference.md)