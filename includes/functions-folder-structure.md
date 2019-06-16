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
ms.openlocfilehash: 9f74365f3fe935be45fa9c45e5b12c45b97b2f8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068453"
---
O código para todas as funções num aplicativo de função específica está localizado numa pasta de projeto de raiz que contém um ficheiro de configuração de anfitrião e as subpastas de um ou mais. Cada subpasta contém o código para uma função distinta, tal como a seguinte representação:

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

O [Host. JSON](../articles/azure-functions/functions-host-json.md) arquivo, que contém algumas configurações específicas do tempo de execução, está na pasta raiz da aplicação de função. A `bin` pasta contém pacotes e outros ficheiros de biblioteca necessários para a aplicação de funções. Consulte os requisitos de idioma específico para um projeto de aplicação de função:

* [C# biblioteca de classes (arquivo. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script do c# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#script](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



