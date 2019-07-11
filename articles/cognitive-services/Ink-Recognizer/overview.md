---
title: O que é o reconhecedor de tinta? -API do reconhecedor de tinta
titleSuffix: Azure Cognitive Services
description: Integre o reconhecedor de tinta em seus aplicativos, Web sites, ferramentas e outras soluções para permitir que os dados de traços de tinta ser identificados e utilizada como entrada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 95121523c510e72894086740ad340bdfd33f3c32
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721395"
---
# <a name="what-is-the-ink-recognizer-api"></a>O que é a API do Reconhecedor de Tinta Digital?


O serviço cognitivo do reconhecedor de tinta fornece uma API de REST com base na cloud para analisar e a reconhecer conteúdo de tinta digital. Ao contrário dos serviços que utilizam o reconhecimento Ótico de carateres (OCR), a API requer dados de traços de tinta digital como entrada. Traços de tinta digital são ordenados por tempo conjuntos de pontos 2D (coordenadas X, Y), que representam o movimento das ferramentas de entrada como canetas digital ou dedos. Em seguida, reconhece as formas e manuscritos conteúdo da entrada e retorna uma resposta JSON que contém entidades tudo reconhecidas.

![Um fluxograma que descreve o envio de uma entrada de traços de tinta para a API](media/ink-recognizer-pen-graph.png)

## <a name="features"></a>Funcionalidades

Com a API do reconhecedor de tinta, pode reconhecer facilmente conteúdo manuscritos em seus aplicativos. 

|Funcionalidade  |Descrição  |
|---------|---------|
| Reconhecimento de escrita manual | Reconhecer conteúdo manuscritos em 63 core [idiomas e localidades](language-support.md). | 
| Reconhecimento de layout | Obtenha informações estruturais sobre o conteúdo de tinta digital. Divida o conteúdo em escrever regiões, parágrafos, linhas, palavras, listas com marcas. Seus aplicativos, em seguida, podem utilizar as informações de esquema para criar recursos adicionais, como a lista automática de formatação e moldar o alinhamento. |
| Reconhecimento de formas | Reconhecer o mais utilizado [formas geométricas](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) quando fazendo anotações. |
| Formas combinadas e reconhecimento de texto | Reconhecer que tinta traços pertencem a formas ou de conteúdo de manuscrito e separadamente classificá-los.|

## <a name="workflow"></a>Fluxo de trabalho

A API do reconhecedor de tinta é um serviço RESTful web, facilitando a chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Depois de se inscrever:

1. Leve os seus dados de traços de tinta e [formatá-lo](concepts/send-ink-data.md#sending-ink-data) no JSON válido.
1. Envie um pedido para a API do reconhecedor de tinta com os seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos Seguintes

Tente um início rápido nos seguintes idiomas para começar a fazer chamadas à API do reconhecedor de tinta.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Para ver como a API de reconhecimento de tinta funciona num aplicativo de escrita a tinta digital, veja as seguintes aplicações de exemplo no GitHub:
* [C# e Plataforma Universal do Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicação de browser Javascript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicação para dispositivos móveis Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicação para dispositivos móveis Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
