---
title: Aplicação Web que chamadas de web APIs (descrição geral) - a plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação web que chamadas de web APIs (descrição geral)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076304"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Cenário: Aplicação Web que chama as APIs Web

Saiba como criar uma aplicação web aos utilizadores iniciar sessão na plataforma de identidade da Microsoft e que chama as APIs web em nome do utilizador com sessão iniciada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Este cenário supposes que já percorreu o seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicação Web que os utilizadores inicia sessão](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Descrição geral

Adicionar autenticação à sua aplicação Web, que, por conseguinte, pode iniciar sessão dos utilizadores e chama uma API web em nome do utilizador com sessão iniciada.

![Aplicação Web que chama as APIs Web](./media/scenario-webapp/web-app.svg)

Aplicações Web que chama a APIs web:

- São aplicativos cliente confidencial.
- é por isso que eles registou um segredo (palavra-passe de aplicação ou certificado) com o Azure AD. Este segredo é passado durante a chamada para o Azure AD para obter um token

## <a name="specifics"></a>Informações específicas

> [!NOTE]
> Adicionar início de sessão numa aplicação Web não utiliza as bibliotecas MSAL como se trata sobre como proteger a aplicação Web. Proteger bibliotecas é obtida pelas bibliotecas com o nome Middleware. Esse foi o objeto do cenário anterior [aos utilizadores iniciar sessão numa aplicação Web](scenario-web-app-sign-user-overview.md)
>
> Ao chamar web APIs a partir de uma aplicação Web, terá de obter os tokens de acesso para essas APIs web. Pode utilizar bibliotecas MSAL para adquirir esses tokens.

A experiência de ponta a ponta de desenvolvedores para este cenário tem, por conseguinte, aspectos específicos, como:

- Durante a [registo de aplicação](scenario-web-app-call-api-app-registration.md), terá de fornecer uma, ou várias (se implementar a sua aplicação para vários locais) responder URIs, segredos ou certificados devem ser compartilhados com o Azure AD.
- O [configuração da aplicação](scenario-web-app-call-api-app-configuration.md) tem de fornecer credenciais de cliente como partilhado com o Azure AD durante o registo de aplicação

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Registo da aplicação](scenario-web-app-call-api-app-registration.md)
