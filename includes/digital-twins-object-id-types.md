---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: cb43c8b8c952d8db6cf450a7015c22c85e7fe4b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76268302"
---
O `objectIdType` (ou tipo de identificador de **objeto)** refere-se ao tipo de identidade que é dado a um papel. Além `DeviceId` dos `UserDefinedFunctionId` tipos e tipos, os tipos de identificador de objetos correspondem a propriedades de objetos do Diretório Ativo Azure.

A tabela seguinte contém os tipos de identificador de objetos suportados em Gémeos Digitais Azure:

| Tipo | Descrição |
| --- | --- |
| IDUtilizador | Atribui um papel a um utilizador. |
| DeviceId | Atribui um papel a um dispositivo. |
| DomainName | Atribui um papel a um nome de domínio. Cada utilizador com o nome de domínio especificado tem os direitos de acesso da função correspondente. |
| TenantId | Atribui um papel a um inquilino. Cada utilizador que pertença ao ID de inquilino azure especificado tem os direitos de acesso da função correspondente. |
| Serviço Principado | Atribui uma função a um id de objeto principal de serviço. |
| Funtida definida pelo utilizador | Atribui uma função a uma função definida pelo utilizador (UDF). |