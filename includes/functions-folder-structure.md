---
title: incluir ficheiro
description: incluir ficheiro
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018450"
---
O código para todas as funções numa aplicação de função específica está localizado numa pasta de projeto raiz que contém um ficheiro de configuração do anfitrião e uma ou mais sub-dobras. Cada sub-dobradeira contém o código para uma função separada. A estrutura da pasta é mostrada na seguinte representação:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Na versão 2.x e superior do tempo de execução de Funções, todas as funções na aplicação de função devem partilhar a mesma pilha de idiomas.  

A [host.jsno](../articles/azure-functions/functions-host-json.md) ficheiro contém configurações específicas do tempo de execução e está na pasta raiz da aplicação de função. Uma pasta *de lixo* contém pacotes e outros ficheiros da biblioteca que a aplicação de função requer. Consulte os requisitos específicos da linguagem para um projeto de aplicação de função:

* [Biblioteca de classes C#(.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Roteiro F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
