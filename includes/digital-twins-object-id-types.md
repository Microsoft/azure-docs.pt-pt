---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307235"
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