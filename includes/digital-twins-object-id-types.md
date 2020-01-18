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
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268302"
---
O `objectIdType` (ou o **tipo de identificador de objeto**) refere-se ao tipo de identidade que é fornecido a uma função. Além dos tipos `DeviceId` e `UserDefinedFunctionId`, os tipos de identificadores de objetos correspondem às propriedades de objetos Azure Active Directory.

A tabela a seguir contém os tipos de identificadores de objeto com suporte no gêmeos digital do Azure:

| Tipo | Descrição |
| --- | --- |
| UserId | Atribui uma função a um usuário. |
| DeviceId | Atribui uma função a um dispositivo. |
| DomainName | Atribui uma função a um nome de domínio. Cada usuário com o nome de domínio especificado tem os direitos de acesso da função correspondente. |
| TenantId | Atribui uma função a um locatário. Cada usuário que pertence à ID de locatário do Azure AD especificado tem os direitos de acesso da função correspondente. |
| ServicePrincipalId | Atribui uma função a uma ID de objeto de entidade de serviço. |
| UserDefinedFunctionId | Atribui uma função a uma UDF (função definida pelo usuário). |