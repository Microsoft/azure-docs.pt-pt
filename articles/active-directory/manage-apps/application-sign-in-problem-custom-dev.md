---
title: Problemas de adesão a uma aplicação desenvolvida sob medida Microsoft Docs
description: Erros comuns que podem estar a fazer com que não consigas assinar uma aplicação que desenvolveste com a Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdfc2a39c6bd3b68df7feb978d2548ad67631235
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84759135"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemas de adesão a uma aplicação desenvolvida sob medida

Existem vários erros que podem estar a fazer com que não consigas assinar uma aplicação. A maior razão pela qual as pessoas se deparam com este problema são as aplicações mal configuradas.

## <a name="errors-related-to--misconfigured-apps"></a>Erros relacionados com aplicações mal configuradas

* Verifique ambas as configurações no portal que tem na sua aplicação. Especificamente, compare o ID do Cliente/Aplicação, URLs de Resposta, Segredos/Chaves do Cliente e ID URI de aplicações.

* Compare o recurso a que está a solicitar acesso em código com as permissões configuradas no separador **Recursos Necessários** para se certificar de que apenas solicita recursos configurados.

* Consulte [o Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) para obter quaisquer erros ou problemas semelhantes.

## <a name="next-steps"></a>Passos seguintes

[Guia de desenvolvedores de anúncios Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentir e integrar apps para a Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Permissões e consentimento no ponto final da plataforma de identidades da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
