---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827718"
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