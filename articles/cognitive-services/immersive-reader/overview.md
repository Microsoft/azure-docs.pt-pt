---
title: O que é a API imersiva do leitor?
titleSuffix: Azure Cognitive Services
description: A API imersiva do leitor é uma ferramenta que pode ser usada para acomodar pessoas com diferenças de aprendizagem ou ajudar novos leitores e alunos de línguas.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 5acd6e4be178c7e645eb6855a79cd0f58303ba9a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846415"
---
# <a name="what-is-immersive-reader"></a>O que é a Leitura Avançada?

O [Leitor Imersivo](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente projetada que implementa técnicas comprovadas para melhorar a compreensão da leitura para leitores emergentes, aprendizes de línguas e pessoas com diferenças de aprendizagem como dislexia.

Pode utilizar a Leitura Avançada na sua aplicação Web com o SDK da Leitura Avançada.

## <a name="what-does-immersive-reader-do"></a>O que faz o Leitor Imersivo?

O Leitor Imersivo foi concebido para tornar a leitura mais acessível a todos.

* Mostra conteúdo numa visão de leitura mínima

  ![Leitura Avançada](./media/immersive-reader.png)

* Exibe imagens de palavras comumente usadas

  ![Dicionário de Imagem](./media/picture-dictionary.png)

* Substantivos de destaque, verbos, adjetivos e advérbios

  ![Partes do Discurso](./media/parts-of-speech.png)

* Lê o seu conteúdo em voz alta para si

  ![Leia Em voz Alta](./media/read-aloud.png)

* Traduz o seu conteúdo para outra língua

  ![Tradução](./media/translation.png)

* Decompõe palavras em silabáveis

  ![Syllabificação](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Como funciona o Leitor Imersivo?

O Leitor Imersivo é uma aplicação web autónoma que, quando invocada através do Leitor Imersivo JavaScript SDK, é exibida em cima da sua aplicação web existente através de um `iframe`. Quando liga para a API para lançar o Leitor Imersivo, especifica o conteúdo que pretende mostrar no Leitor Imersivo. O nosso SDK lida com a criação e o estilo da `iframe` e comunicação com o serviço de backend Do Leitor Imersivo, que processa o conteúdo para partes da fala, texto à fala, tradução, e assim por diante.

## <a name="next-steps"></a>Passos seguintes

Introdução à Leitura Avançada:

* Salta para o [arranque rápido](./quickstart.md)
* Explore o [SDK do leitor de imersão no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Leia a [referência SDK do leitor imersivo](./reference.md)