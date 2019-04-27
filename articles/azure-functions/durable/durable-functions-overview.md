---
title: Descrição geral de funções duráveis - Azure
description: Introdução para a extensão de funções duráveis para as funções do Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 4c7b4733d05f18d3c30e45fd08c3cf9c50354ebc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730604"
---
# <a name="what-are-durable-functions"></a>O que são funções duráveis?

*Funções duráveis* são uma extensão da [as funções do Azure](../functions-overview.md) que permite que escreva funções com monitoração de estado num ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

## <a name="benefits"></a>Benefícios

A extensão permite-lhe definir fluxos de trabalho com monitorização de estado utilizando um [ *função de orquestrador*](durable-functions-types-features-overview.md#orchestrator-functions), que pode fornecer as seguintes vantagens:

* Pode definir os fluxos de trabalho no código. Não existem esquemas JSON ou designers são necessários.
* Outras funções podem ser chamadas ambos modo síncrono e assíncrono. Saída de funções chamadas pode ser guardada para variáveis locais.
* Curso é automaticamente foi efetuada a verificação quando a função awaits. Estado local, nunca é perdido quando o processo é reciclado ou a VM é reiniciada.

## <a name="application-patterns"></a>Padrões de aplicação

O principal motivo para as funções durável é simplificar os requisitos de coordenação complexa, com monitoração de estado em aplicações sem servidor. Seguem-se alguns padrões de aplicativo típico que podem se beneficiar de funções duráveis:

* [O encadeamento](durable-functions-concepts.md#chaining)
* [Fan-out/fan-in](durable-functions-concepts.md#fan-in-out)
* [APIs de HTTP assíncrono](durable-functions-concepts.md#async-http)
* [Monitorização](durable-functions-concepts.md#monitoring)
* [Interação humana](durable-functions-concepts.md#human)

## <a name="language-support"></a>Idiomas suportados

Funções duráveis suporta atualmente os seguintes idiomas:

* **C#**: ambos [pré-compiladas bibliotecas de classes](../functions-dotnet-class-library.md) e [ C# script](../functions-reference-csharp.md).
* **F#**: pré-compiladas bibliotecas de classes e F# script. F#script só é suportado para a versão 1.x do runtime das funções do Azure.
* **JavaScript**: suportado apenas para a versão 2.x do runtime das funções do Azure. Requer versão 1.7.0 a extensão de funções duráveis, ou uma versão posterior. 

Funções duráveis consistia na meta de suporte a todos [idiomas de funções do Azure](../supported-languages.md). Consulte a [lista de problemas de funções duráveis](https://github.com/Azure/azure-functions-durable-extension/issues) para o estado mais recente de trabalho para oferecer suporte a idiomas adicionais.

Como as funções do Azure, existem modelos para ajudar a desenvolver funções durável usando [Visual Studio 2017](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)e o [portal do Azure](durable-functions-create-portal.md).

## <a name="billing"></a>Faturação

Funções duráveis são faturadas as mesmas que as funções do Azure. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Iniciar de imediato

Pode começar a utilizar com as funções duráveis em menos de 10 minutos, concluindo um destes tutoriais de início rápido de idioma específico:

* [C#utilizar o Visual Studio 2017](durable-functions-create-first-csharp.md)
* [JavaScript com o Visual Studio Code](quickstart-js-vscode.md)

Em ambos os inícios rápidos, localmente criar e testar uma função durável "hello world". Em seguida, publique o código de função no Azure. A função que cria orquestra e está ligado em conjunto de chamadas para outras funções.

## <a name="learn-more"></a>Saiba mais

O vídeo seguinte realça as vantagens de funções duráveis:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Uma vez que as funções durável é uma extensão avançada para [as funções do Azure](../functions-overview.md), não é adequado para todos os aplicativos. Para saber mais sobre as funções durável, veja [padrões de funções duráveis e conceitos técnicos](durable-functions-concepts.md). Para uma comparação com outras tecnologias de orquestração do Azure, veja [comparar funções do Azure e Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Padrões de funções duráveis e conceitos técnicos](durable-functions-concepts.md)
