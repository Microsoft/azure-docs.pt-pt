---
title: Entender a autenticação de API usando o gêmeos digital do Azure | Microsoft Docs
description: Saiba como se conectar e autenticar com APIs usando o gêmeos digital do Azure.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: lyhughes
ms.openlocfilehash: 6d8d6f03847fcd22a6d78c445ae039c73e9badfa
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719157"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Conectar e autenticar com APIs

O Azure digital gêmeos usa Azure Active Directory (Azure AD) para autenticar usuários e proteger aplicativos. O Azure AD dá suporte à autenticação para uma variedade de arquiteturas modernas. Todos eles se baseiam nos protocolos padrão do setor OAuth 2,0 ou OpenID Connect. Além disso, os desenvolvedores podem usar o Azure AD para criar aplicativos de locatário único e de linha de negócios (LOB). Os desenvolvedores também podem usar o Azure AD para desenvolver aplicativos multilocatários.

Para obter uma visão geral do Azure AD, visite a [página conceitos básicos](https://docs.microsoft.com/azure/active-directory/fundamentals/) para obter guias passo a passo, conceitos e inícios rápidos.

> [!TIP]
> Siga o [tutorial](tutorial-facilities-setup.md) para configurar e executar um aplicativo de exemplo gêmeos digital do Azure.

Para integrar uma aplicação ou serviço com o Azure AD, primeiro, o programador deve registar a aplicação no Azure AD. Para obter instruções detalhadas e capturas de tela, consulte este guia de [início rápido](../active-directory/develop/quickstart-register-app.md).

Há suporte para [cinco cenários de aplicativo primário](../active-directory/develop/v2-app-types.md) pelo Azure AD:

* Aplicativo de página única (SPA): Um usuário precisa entrar em um aplicativo de página única protegido pelo Azure AD.
* Navegador da Web para aplicativo Web: Um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD.
* Aplicativo nativo para API da Web: Um aplicativo nativo que é executado em um telefone, Tablet ou PC precisa autenticar um usuário para obter recursos de uma API Web protegida pelo Azure AD.
* Aplicativo Web para API da Web: Um aplicativo Web precisa obter recursos de uma API da Web protegida pelo Azure AD.
* Aplicativo de daemon ou de servidor para API da Web: Um aplicativo daemon ou um aplicativo de servidor sem interface do usuário da Web precisa obter recursos de uma API Web protegida pelo Azure AD.

> [!IMPORTANT]
> O Azure digital gêmeos dá suporte a ambas as bibliotecas de autenticação a seguir:
> * A biblioteca de [autenticação da Microsoft mais recente (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * A [Adal (biblioteca de autenticação de Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chamar gêmeos digital de uma API da Web de camada intermediária

Quando os desenvolvedores arquitetam soluções de gêmeos digital, normalmente criam um aplicativo de camada intermediária ou API. Em seguida, o aplicativo ou a API chama o downstream da API digital gêmeos. Para dar suporte a essa arquitetura de solução da Web padrão, certifique-se de que os usuários primeiro:

1. Autenticar com o aplicativo de camada intermediária

1. Um token em nome de OAuth 2,0 é adquirido durante a autenticação

1. O token adquirido é usado para autenticar ou chamar APIs que são mais downstream usando o fluxo em nome de

Para obter instruções sobre como orquestrar o fluxo em nome de, consulte [fluxo em nome de do OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Você também pode exibir exemplos de código na [chamada de uma API Web downstream](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Passos seguintes

Para configurar e testar o Azure digital gêmeos usando o fluxo de concessão implícita do OAuth 2,0, leia [Configurar o postmaster](./how-to-configure-postman.md).

Para saber mais sobre a segurança de gêmeos digital do Azure, leia [criar e gerenciar atribuições de função](./security-create-manage-role-assignments.md).