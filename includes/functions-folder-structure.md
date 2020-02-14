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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205709"
---
O código para todas as funções em um aplicativo de funções específico está localizado em uma pasta de projeto raiz que contém um arquivo de configuração de host e uma ou mais subpastas. Cada subpasta contém o código para uma função separada. A estrutura de pastas é mostrada na seguinte representação:

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

Na versão 2. x e superior do tempo de execução do functions, todas as funções no aplicativo de funções devem compartilhar a mesma pilha de idiomas.  

O ficheiro [host.json](../articles/azure-functions/functions-host-json.md) contém configurações específicas do tempo de execução e está na pasta raiz da aplicação de função. Uma pasta *de caixotes* contém pacotes e outros ficheiros de biblioteca que a aplicação de função necessita. Consulte os requisitos específicos do idioma para um projeto de aplicativo de funções:

* [C#biblioteca de classes (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#script (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#script](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [python](../articles/azure-functions/functions-reference-python.md#folder-structure)
