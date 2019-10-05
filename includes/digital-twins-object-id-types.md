---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 12d4278171d43fdaf8613a1c91bdbffc269adc56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949060"
---
O `objectIdType` (ou **tipo de identificador de objeto**) refere-se ao tipo de identidade que é fornecido a uma função. Além dos tipos `DeviceId` e `UserDefinedFunctionId`, os tipos de identificadores de objetos correspondem às propriedades de objetos de Azure Active Directory.

A tabela a seguir contém os tipos de identificadores de objeto com suporte no gêmeos digital do Azure:

| Type | Descrição |
| --- | --- |
| UserId | Atribui uma função a um usuário. |
| DeviceId | Atribui uma função a um dispositivo. |
| DomainName | Atribui uma função a um nome de domínio. Cada usuário com o nome de domínio especificado tem os direitos de acesso da função correspondente. |
| TenantId | Atribui uma função a um locatário. Cada usuário que pertence à ID de locatário do Azure AD especificado tem os direitos de acesso da função correspondente. |
| ServicePrincipalId | Atribui uma função a uma ID de objeto de entidade de serviço. |
| UserDefinedFunctionId | Atribui uma função a uma UDF (função definida pelo usuário). |