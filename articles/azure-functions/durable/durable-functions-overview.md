---
title: Visão geral de Durable Functions-Azure
description: Introdução à extensão de Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 323ab530d8199dd154e5d3568c09f86f6f52d702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087203"
---
# <a name="what-are-durable-functions"></a>O que são Durable Functions?

*Durable Functions* são uma extensão de [Azure Functions](../functions-overview.md) que permite que você escreva funções com estado em um ambiente sem servidor. A extensão gere o estado, os pontos de verificação e os reinícios por si.

## <a name="benefits"></a>Benefícios

A extensão permite que você defina fluxos de trabalho com estado usando uma [*função*](durable-functions-types-features-overview.md#orchestrator-functions)de orquestrador, que pode fornecer os seguintes benefícios:

* Você pode definir seus fluxos de trabalho no código. Não são necessários esquemas ou designers JSON.
* Outras funções podem ser chamadas de forma síncrona e assíncrona. A saída das funções chamadas pode ser salva em variáveis locais.
* O progresso é automaticamente marcado quando a função aguarda. O estado local nunca é perdido quando o processo é reciclado ou a VM é reinicializada.

## <a name="application-patterns"></a>Padrões de aplicação

O caso de uso primário para o Durable Functions é simplificar os requisitos complexos de coordenação com estado em aplicativos sem servidor. Veja a seguir alguns padrões de aplicativo típicos que podem se beneficiar do Durable Functions:

* [Encadeamento](durable-functions-concepts.md#chaining)
* [Fan-out/Fan-in](durable-functions-concepts.md#fan-in-out)
* [APIs HTTP assíncronas](durable-functions-concepts.md#async-http)
* [Monitorização](durable-functions-concepts.md#monitoring)
* [Interação humana](durable-functions-concepts.md#human)

## <a name="language-support"></a>Idiomas com suporte

O Durable Functions atualmente dá suporte aos seguintes idiomas:

* **C#** : as [bibliotecas de classes pré-compiladas](../functions-dotnet-class-library.md) e [ C# o script](../functions-reference-csharp.md).
* **F#** : script e bibliotecas de F# classes pré-compiladas. F#o script só tem suporte para a versão 1. x do tempo de execução de Azure Functions.
* **JavaScript**: com suporte apenas para a versão 2. x do tempo de execução de Azure functions. Requer a versão 1.7.0 da extensão de Durable Functions ou uma versão posterior. 

Durable Functions tem o objetivo de dar suporte a todas as linguagens de [Azure Functions](../supported-languages.md). Consulte a [lista de problemas de durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) para obter o status do trabalho mais recente para dar suporte a idiomas adicionais.

Assim como Azure Functions, há modelos para ajudá-lo a desenvolver Durable Functions usando o [Visual Studio 2019](durable-functions-create-first-csharp.md), o [Visual Studio Code](quickstart-js-vscode.md)e o [portal do Azure](durable-functions-create-portal.md).

## <a name="billing"></a>Faturação

Durable Functions são cobradas da mesma forma que Azure Functions. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Ir direto para a direita

Você pode começar a usar o Durable Functions em menos de 10 minutos, concluindo um destes tutoriais de início rápido específicos do idioma:

* [C#usando o Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript usando Visual Studio Code](quickstart-js-vscode.md)

Em ambos os guias de início rápido, você cria e testa localmente uma função durável "Olá, mundo". Em seguida, publique o código de função no Azure. A função que você cria orquestra e encadeia chamadas para outras funções.

## <a name="learn-more"></a>Saber mais

O vídeo a seguir destaca os benefícios de Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Como Durable Functions é uma extensão avançada para [Azure Functions](../functions-overview.md), ela não é apropriada para todos os aplicativos. Para saber mais sobre Durable Functions, consulte [padrões de durable Functions e conceitos técnicos](durable-functions-concepts.md). Para obter uma comparação com outras tecnologias de orquestração do Azure, consulte [comparar Azure Functions e aplicativos lógicos do Azure](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Padrões de Durable Functions e conceitos técnicos](durable-functions-concepts.md)
