---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795902"
---
O `objectIdType` (ou **tipo de identificador de objeto**) refere-se para o tipo de identidade, que é atribuído a uma função. Apart a partir da `DeviceId` e `UserDefinedFunctionId` tipos, os tipos de identificador de objeto correspondem às propriedades de objetos do Active Directory do Azure.

A tabela seguinte contém os tipos de identificador de objeto suportados no duplos Digital do Azure:

| Tipo | Descrição |
| --- | --- |
| UserId | Atribui uma função a um utilizador. |
| DeviceId | Atribui uma função a um dispositivo. |
| DomainName | Atribui uma função a um nome de domínio. Cada utilizador com o nome de domínio tem os direitos de acesso de função correspondente. |
| TenantId | Atribui uma função a um inquilino. Cada utilizador que pertence ao especificado ID de inquilino do Azure AD tem os direitos de acesso de função correspondente. |
| servicePrincipalId | Atribui uma função a um ID de objeto do principal de serviço. |
| UserDefinedFunctionId | Atribui uma função a uma função definida pelo utilizador (UDF). |