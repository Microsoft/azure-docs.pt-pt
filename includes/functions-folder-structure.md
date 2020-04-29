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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77205709"
---
O código para todas as funções de uma aplicação de função específica está localizado numa pasta de projeto de raiz que contém um ficheiro de configuração de hospedeiro e uma ou mais subpastas. Cada subpasta contém o código para uma função separada. A estrutura da pasta é mostrada na seguinte representação:

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

Na versão 2.x e superior ao tempo de funcionamento das Funções, todas as funções na aplicação de função devem partilhar a mesma pilha de idiomas.  

O ficheiro [host.json](../articles/azure-functions/functions-host-json.md) contém configurações específicas do tempo de execução e está na pasta raiz da aplicação de função. Uma pasta *de caixotes* contém pacotes e outros ficheiros de biblioteca que a aplicação de função necessita. Consulte os requisitos específicos do idioma para um projeto de aplicação de funções:

* [Biblioteca de classe C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Roteiro F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
