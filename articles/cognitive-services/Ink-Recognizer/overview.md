---
title: O que é Reconhecimento de Tinta? - API reconhecedor de tinta
titleSuffix: Azure Cognitive Services
description: Integre o Reconhecedor de Tinta nas suas aplicações, websites, ferramentas e outras soluções para permitir a identificação e uso de dados de traçado de tinta como entrada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448155"
---
# <a name="what-is-the-ink-recognizer-api"></a>O que é a API do Reconhecedor de Tinta Digital?


O Serviço Cognitivo do Reconhecimento de Tinta fornece uma API REST baseada na nuvem para analisar e reconhecer o conteúdo de tinta digital. Ao contrário dos serviços que utilizam o Reconhecimento Ótico de Caracteres (OCR), a API requer dados de traçado de tinta digital como entrada. Os traços de tinta digital são conjuntos de pontos 2D (coordenadas X,Y) que representam o movimento de ferramentas de entrada, como canetas digitais ou dedos. Em seguida, reconhece as formas e conteúdo manuscrito da entrada e devolve uma resposta JSON contendo todas as entidades reconhecidas.

![Um fluxograma descrevendo o envio de uma entrada de tacada de tinta para a API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funcionalidades

Com a API do Reconhecimento de Tinta, pode facilmente reconhecer conteúdo manuscrito nas suas aplicações. 

|Funcionalidade  |Descrição  |
|---------|---------|
| Reconhecimento de caligrafia | Reconheça o conteúdo manuscrito em 63 [línguas e locais](language-support.md)fundamentais. | 
| Reconhecimento de layout | Obtenha informações estruturais sobre o conteúdo de tinta digital. Dividir o conteúdo em regiões de escrita, parágrafos, linhas, palavras, listas de balas. As suas aplicações podem então utilizar as informações de layout para construir funcionalidades adicionais como formatação automática de listas e alinhamento de forma. |
| Reconhecimento de formas | Reconheça as [formas geométricas](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) mais usadas ao tomar notas. |
| Formas combinadas e reconhecimento de texto | Reconheça quais os traços de tinta que pertencem a formas ou conteúdo manuscrito, e classifica-os separadamente.|

## <a name="workflow"></a>Fluxo de trabalho

O API reconhecedor de tinta é um serviço web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar a JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Depois de se inscrever:

1. Leve os dados do traçado de tinta e [forme-os](concepts/send-ink-data.md#sending-ink-data) em JSON válido.
1. Envie um pedido para a API do Reconhecedor de Tinta com os seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Tente um início rápido nas seguintes línguas para começar a fazer chamadas para a API do Reconhecimento de Tinta.
* [C #](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Para ver como funciona a API de Reconhecimento de Tinta numa aplicação de tinta digital, veja as seguintes aplicações de amostra no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
