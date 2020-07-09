---
title: O que é a API do Leitor Imersivo?
titleSuffix: Azure Cognitive Services
description: A Imersiva API reader é uma ferramenta que pode ser usada para acomodar pessoas com diferenças de aprendizagem ou ajudar novos leitores e alunos de línguas.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: b9efe70e8658e25d61decffbe44dec776890b17b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267278"
---
# <a name="what-is-immersive-reader"></a>O que é a Leitura Avançada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O [Leitor Imersivo](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente concebida que implementa técnicas comprovadas para melhorar a compreensão da leitura para leitores emergentes, aprendizes de línguas e pessoas com diferenças de aprendizagem como a dislexia.

Pode utilizar a Leitura Avançada na sua aplicação Web com o SDK da Leitura Avançada.

## <a name="what-does-immersive-reader-do"></a>O que faz o Leitor Imersivo?

O Leitor Imersivo foi concebido para tornar a leitura mais acessível para todos.

* Mostra conteúdo numa vista de leitura mínima

  ![Leitura Avançada](./media/immersive-reader.png)

* Exibe imagens de palavras comumente usadas

  ![Dicionário de imagem](./media/picture-dictionary.png)

* Destaques substantivos, verbos, adjetivos e advéres

  ![Partes do Discurso](./media/parts-of-speech.png)

* Lê o seu conteúdo em voz alta

  ![Ler em voz alta](./media/read-aloud.png)

* Traduz o seu conteúdo para outra língua

  ![Tradução](./media/translation.png)

* Decompõe as palavras em sílabas

  ![Syllabificação](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Como funciona o Leitor Imersivo?

O Leitor Imersivo é uma aplicação web autónoma que, quando invocada através do Leitor Imersivo JavaScript SDK, é apresentada em cima da sua aplicação web existente através de um `iframe` . Quando liga para a API para lançar o Leitor Imersivo, especifica o conteúdo que pretende mostrar no Leitor Imersivo. O nosso SDK lida com a criação e estilo do serviço de `iframe` backend do Leitor Imersivo, que processa o conteúdo de partes de fala, texto para fala, tradução, e assim por diante.

## <a name="next-steps"></a>Passos seguintes

Introdução à Leitura Avançada:

* Salte para os [arranques rápidos](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
* Explore o [SDK do leitor imersivo no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Leia a [referência SDK do leitor imersivo](./reference.md)
