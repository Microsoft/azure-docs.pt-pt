---
title: Como encontrar uma API específica necessária para uma aplicação personalizada desenvolvida | Documentos da Microsoft
description: Como configurar as permissões que necessárias para aceder a uma API específica em seu custom desenvolveu a aplicação do Azure AD
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
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 306d16dd306fc569181e0334e6674a9c935aac1f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182226"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Como encontrar uma API específica necessária para uma aplicação personalizada desenvolvida

O acesso a APIs exigem a configuração das funções e âmbitos de acesso. Se quiser expor seu recurso aplicação APIs web para aplicativos cliente, terá de configurar âmbitos de acesso e as funções para a API. Se pretender que uma aplicação de cliente para aceder a uma API web, tem de configurar permissões para aceder à API do registo de aplicação.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar uma aplicação de recurso para expor APIs Web

Quando expõe a web API, a API seja apresentado no **selecionar uma API** lista ao adicionar permissões a um registo de aplicações. Para adicionar âmbitos de acesso, siga os passos descritos em [adicionando os âmbitos de acesso à sua aplicação de recurso](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurar uma aplicação de cliente para aceder a APIs web

Quando adicionar permissões ao registo da aplicação, pode **adicionar acesso à API** para APIs da web expostas. Para aceder a web APIs, siga os passos descritos em [adicionar credenciais ou permissões para aceder a web APIs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="next-steps"></a>Passos Seguintes

-   [Integração de aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Compreender o manifesto da aplicação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


