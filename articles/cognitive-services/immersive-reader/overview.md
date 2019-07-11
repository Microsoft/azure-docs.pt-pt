---
title: O que é a API de leitor de imersão?
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre o leitor de imersão de API.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 4500b6213c549ab9977fe8f2d849ffa8089d04b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718432"
---
# <a name="what-is-immersive-reader"></a>O que é a Leitura Avançada?

O [leitor envolventes](https://www.onenote.com/learningtools) é uma ferramenta projetada, inclusivamente que implementa técnicas comprovadas para melhorar a compreensão de leitura para os leitores emergentes, aprendizes de linguagem e as pessoas com diferenças como dyslexia de aprendizado.

Pode utilizar a Leitura Avançada na sua aplicação Web com o SDK da Leitura Avançada.

## <a name="what-does-immersive-reader-do"></a>O que faz o leitor de imersão?

O leitor de imersão foi projetado para fazer a leitura mais acessível para todos os utilizadores.

* Mostra o conteúdo numa vista de leitura mínimo

  ![Leitura Avançada](./media/immersive-reader.png)

* Exibe figuras de palavras usadas

  ![Dicionário da imagem](./media/picture-dictionary.png)

* Realça os substantivos, verbos, os adjetivos e adverbs

  ![Partes de voz](./media/parts-of-speech.png)

* Lê o conteúdo em voz alta para si

  ![Leitura em voz alta](./media/read-aloud.png)

* Converte o seu conteúdo em outro idioma

  ![Tradução](./media/translation.png)

* Divide as palavras em sílabas

  ![Syllabification](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Como funciona o leitor de imersão?

O leitor de imersão é uma aplicação autónoma e web que, quando invocado utilizando o SDK de JavaScript do leitor Imersivos, é apresentada em cima da sua aplicação web existente através de um `iframe`. Quando chama a API para iniciar o leitor Imersivos, especificar o conteúdo que pretende mostrar no leitor envolvente e experimental. O SDK processa a criação e o estilo do `iframe` e a comunicação com o serviço de back-end do leitor envolvente, que processa o conteúdo de partes de voz, texto em voz, tradução e assim por diante.

## <a name="next-steps"></a>Passos Seguintes

Introdução à Leitura Avançada:

* Ir para o [início rápido](./quickstart.md)
* Explorar o [leitor envolvente SDK no GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* Leia o [envolventes leitor a referência do SDK](./reference.md)