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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594546"
---
O código para todas as funções num aplicativo de função específica está localizado numa pasta de projeto de raiz que contém um ficheiro de configuração de anfitrião e as subpastas de um ou mais. Cada subpasta contém o código para uma função separada. A estrutura de pastas é mostrada na seguinte representação:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Na versão 2.x do runtime das funções, todas as funções na aplicação de função têm de partilhar a mesma pilha de idioma.  

O [Host. JSON](../articles/azure-functions/functions-host-json.md) ficheiro contém configurações específicas do tempo de execução e está na pasta raiz da aplicação de função. R *bin* pasta contém pacotes e outros ficheiros de biblioteca que requer que a aplicação de funções. Consulte os requisitos de idioma específico para um projeto de aplicação de função:

* [C# biblioteca de classes (arquivo. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script do c# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#script](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



