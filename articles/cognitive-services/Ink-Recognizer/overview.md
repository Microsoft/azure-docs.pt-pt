---
title: O que é O Reconhecedor de Tinta? - API do Reconhecimento de Tintas
titleSuffix: Azure Cognitive Services
description: Integre o Reconhecimento de Tinta nas suas aplicações, websites, ferramentas e outras soluções para permitir que os dados de traçado de tinta sejam identificados e utilizados como entrada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c1a720e7e9bd9c71f925f104ca7fc70a1a5ef59
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051071"
---
# <a name="what-is-the-ink-recognizer-api"></a>O que é a API do Reconhecedor de Tinta Digital?

[!INCLUDE [ink-recognizer-deprecation](includes/deprecation-note.md)]

O Serviço Cognitivo Do Reconhecimento de Tintas fornece uma API DE REST baseada na nuvem para analisar e reconhecer o conteúdo de tinta digital. Ao contrário dos serviços que utilizam o Reconhecimento de Caracteres Óticos (OCR), a API requer dados digitais de traçado de tinta como entrada. Os traços de tinta digital são conjuntos de pontos 2D (coordenadas X,Y) que representam o movimento de ferramentas de entrada como canetas digitais ou dedos. Em seguida, reconhece as formas e o conteúdo manuscrito da entrada e devolve uma resposta JSON contendo todas as entidades reconhecidas.

![Um fluxograma descrevendo o envio de uma entrada de traçado de tinta para a API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funcionalidades

Com a API do Reconhecimento de Tinta, pode facilmente reconhecer conteúdo manuscrito nas suas aplicações. 

|Funcionalidade  |Descrição  |
|---------|---------|
| Reconhecimento de caligrafia | Reconhecer conteúdo manuscrito em 63 [idiomas e locais principais.](language-support.md) | 
| Reconhecimento de layout | Obtenha informações estruturais sobre o conteúdo da tinta digital. Parta o conteúdo em regiões escritas, parágrafos, linhas, palavras, listas de balas. As suas aplicações podem então utilizar as informações de layout para construir funcionalidades adicionais, como formatação automática de listas e alinhamento de formas. |
| Reconhecimento de forma | Reconheça as formas [geométricas](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) mais usadas ao tomar notas. |
| Formas combinadas e reconhecimento de texto | Reconheça quais traços de tinta pertencem a formas ou conteúdo manuscrito, e classifique-os separadamente.|

## <a name="workflow"></a>Fluxo de trabalho

A API do Reconhecimento de Tinta é um serviço web RESTful, facilitando a chamada a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar o JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Depois de se inscrever:

1. Pegue os seus dados de traçado de tinta e [formate-os](concepts/send-ink-data.md#sending-ink-data) em JSON válido. A API aceita até 1500 traçados de tinta por pedido. 
1. Envie um pedido à API do Reconhecimento de Tinta com os seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Experimente um arranque rápido nas seguintes línguas para começar a fazer chamadas para a API do Reconhecimento de Tinta.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Para ver como funciona a API de Reconhecimento de Tinta numa aplicação digital de tinta, veja as seguintes aplicações de amostra no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
