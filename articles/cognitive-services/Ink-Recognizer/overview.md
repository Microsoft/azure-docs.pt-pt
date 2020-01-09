---
title: O que é o reconhecedor de tinta? -API do reconhecedor de tinta
titleSuffix: Azure Cognitive Services
description: Integre o reconhecedor de tinta em seus aplicativos, sites, ferramentas e outras soluções para permitir que os dados de traço de tinta sejam identificados e usados como entrada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448155"
---
# <a name="what-is-the-ink-recognizer-api"></a>O que é a API do Reconhecedor de Tinta Digital?


O serviço cognitiva do reconhecedor de tinta fornece uma API REST baseada em nuvem para analisar e reconhecer o conteúdo de tinta digital. Ao contrário dos serviços que usam o OCR (reconhecimento óptico de caracteres), a API requer dados de traço de tinta digital como entrada. Os traços de tinta digital são conjuntos ordenados por tempo de pontos 2D (coordenadas X, Y) que representam o movimento das ferramentas de entrada, como canetas digitais ou dedos. Em seguida, ele reconhece as formas e o conteúdo manuscrito da entrada e retorna uma resposta JSON que contém todas as entidades reconhecidas.

![Um fluxograma que descreve o envio de uma entrada de traço de tinta para a API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funcionalidades

Com a API do reconhecedor de tinta, você pode facilmente reconhecer conteúdo manuscrito em seus aplicativos. 

|Funcionalidade  |Descrição  |
|---------|---------|
| Reconhecimento de escrita manual | Reconheça o conteúdo manuscrito em [idiomas e localidades](language-support.md)63 principais. | 
| Reconhecimento de layout | Obtenha informações estruturais sobre o conteúdo de tinta digital. Quebre o conteúdo em regiões de escrita, parágrafos, linhas, palavras, listas com marcadores. Em seguida, seus aplicativos podem usar as informações de layout para criar recursos adicionais como formatação de lista automática e alinhamento de forma. |
| Reconhecimento de formas | Reconheça as [formas geométricas](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) usadas com mais frequência ao fazer anotações. |
| Formas combinadas e reconhecimento de texto | Reconheça quais traços de tinta pertencem a formas ou conteúdo manuscrito e classifique-as separadamente.|

## <a name="workflow"></a>Fluxo de Trabalho

A API do reconhecedor de tinta é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar o JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Após a inscrição:

1. Pegue seus dados de traço de tinta e [formate-](concepts/send-ink-data.md#sending-ink-data) os em JSON válido.
1. Envie uma solicitação para a API do reconhecedor de tinta com seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Experimente um início rápido nos idiomas a seguir para começar a fazer chamadas para a API do reconhecedor de tinta.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Para ver como a API de reconhecimento de tinta funciona em um aplicativo de escrita digital, dê uma olhada nos seguintes aplicativos de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
