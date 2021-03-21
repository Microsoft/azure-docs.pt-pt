---
author: baanders
description: incluir ficheiros que descrevem como validar os modelos Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025487"
---
> [!TIP]
> Depois de criar um modelo, é recomendável validar os seus modelos offline antes de os enviar para a sua instância Azure Digital Twins.

Existe uma amostra agnóstica linguística disponível para validar documentos de modelo para garantir que o DTDL está correto antes de o enviar para o seu caso. Está localizado aqui: [**Amostra de Validador DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* A amostra validador DTDL é construída numa biblioteca de parser DTDL .NET, que está disponível no NuGet como biblioteca do lado do cliente: [**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Também pode usar a biblioteca diretamente para desenhar a sua própria solução de validação. Ao utilizar a biblioteca parser, certifique-se de que utiliza uma versão compatível com a versão que o Azure Digital Twins está a executar. Atualmente, esta é a versão *3.12.4*.

Pode saber mais sobre a amostra validador e a biblioteca de parser, incluindo exemplos de utilização, em [*Como-a-fazer: Parse e validar modelos*](../articles/digital-twins/how-to-parse-models.md).