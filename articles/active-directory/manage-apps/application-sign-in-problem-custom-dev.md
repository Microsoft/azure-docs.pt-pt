---
title: Problemas ao iniciar sessão a uma aplicação personalizada desenvolvida | Documentos da Microsoft
description: Rrors comuns que pode estar a causar a não conseguir iniciar sessão numa aplicação desenvolvidos com o Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4365c87f0028fe3a9c4ba35f40599359eb1455f
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259130"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemas ao iniciar sessão a uma aplicação personalizada desenvolvida

Há vários erros que podem estar a causar a não conseguir iniciar sessão numa aplicação. O maior encontro de pessoas de motivo que esse problema é configurado incorretamente aplicações.

## <a name="errors-related-to--misconfigured-apps"></a>Erros relacionados com aplicações configurado incorretamente

* Certifique-se de que ambas as configurações no portal do corresponde ao que tem na sua aplicação. Especificamente, compare o ID de cliente/aplicação, URLs de resposta, chaves/segredos de cliente e URI de ID de aplicação.

* Comparar o recurso que está a solicitar acesso ao código com as permissões configuradas no **necessários recursos** separador para se certificar de que apenas a solicitar recursos que configurou.

* Ver [Stack Overflow do Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory) para quaisquer erros ou problemas semelhantes.

## <a name="next-steps"></a>Passos Seguintes

[Guia do programador do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentimento e a integração de aplicações para o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Aplicações de convergência de consentimento e concessão de permissões para o Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[O Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
