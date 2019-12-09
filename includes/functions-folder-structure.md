---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fd220a4a955a8b2cd583c2fe9e3899cb36c1587d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935757"
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

O arquivo [host. JSON](../articles/azure-functions/functions-host-json.md) contém configurações específicas de tempo de execução e está na pasta raiz do aplicativo de funções. Uma pasta *bin* contém pacotes e outros arquivos de biblioteca que o aplicativo de funções requer. Consulte os requisitos específicos do idioma para um projeto de aplicativo de funções:

* [C#biblioteca de classes (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script do c# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#prescritiva](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
