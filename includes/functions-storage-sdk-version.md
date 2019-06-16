---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132395"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versão do SDK do armazenamento do Azure nas funções 1.x

As funções, 1.x, os acionadores do armazenamento e ligações de utilizam a versão 7.2.1 do SDK de armazenamento do Azure ([windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) pacote NuGet). Se fazer referência a uma versão diferente do SDK do armazenamento e vincular a um tipo de SDK de armazenamento na sua assinatura de função, o runtime das funções pode comunicar que não é possível vincular a esse tipo. A solução é certificar-se de que suas referências de projeto [windowsazure. Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
