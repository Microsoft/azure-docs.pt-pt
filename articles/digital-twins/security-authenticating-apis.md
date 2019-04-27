---
title: Compreender a autenticação de API de duplos Digital do Azure | Documentos da Microsoft
description: Utilizar duplos Digital do Azure para ligar e autenticar para APIs
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533833"
---
# <a name="connect-and-authenticate-to-apis"></a>Ligar e autenticar para APIs

Duplos Digital do Azure utiliza o Azure Active Directory (Azure AD) para autenticar os utilizadores e proteger as aplicações. O Azure AD suporta a autenticação para uma variedade de modernas arquiteturas. Todos eles são baseiam-se os protocolos de norma da indústria OAuth 2.0 ou OpenID Connect. Além disso, os programadores podem utilizar o Azure AD para a criação de inquilino único e linha de negócio (LOB). Os desenvolvedores também podem utilizar o Azure AD para desenvolver aplicações multi-inquilino.

Para obter uma descrição geral do Azure AD, visite o [página de conceitos básicos](https://docs.microsoft.com/azure/active-directory/fundamentals/index) para guias passo a passo, conceitos e guias de introdução.

Para integrar uma aplicação ou serviço com o Azure AD, primeiro, o programador deve registar a aplicação no Azure AD. Para obter instruções detalhadas e capturas de ecrã, consulte [este guia de introdução](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Cinco cenários de aplicação principal](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) são suportadas pelo Azure AD:

* Aplicação de página única (SPA): O utilizador precisa de iniciar sessão a uma aplicação de página única que está protegida pelo Azure AD.
* Navegador da Web para a aplicação web: O utilizador precisa de iniciar sessão a uma aplicação web que está protegida pelo Azure AD.
* Aplicação nativa a web API: Um aplicativo nativo que é executado num telefone, tablet ou PC tem de autenticar um usuário para obter recursos de uma API web que está protegido pelo Azure AD.
* Aplicação Web a web API: Uma aplicação web tem de obter recursos de uma API web protegida pelo Azure AD.
* Aplicação de daemon ou servidor a web API: Um aplicativo de daemon ou um aplicativo de servidor com nenhum web tem de interface do Usuário obter recursos de uma API web protegida pelo Azure AD.

A biblioteca de autenticação do Windows Azure oferece várias formas de adquirir os tokens de Active Directory. Para obter detalhes sobre os exemplos de código e de biblioteca, consulte [este artigo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chamar duplos Digital a partir de uma API da web de camada intermediária

Quando os desenvolvedores arquitetar soluções digitais duplos, eles normalmente, criaram uma aplicação de camada intermediária ou a API. A aplicação ou a API em seguida, chama a API de duplos Digital downstream. Para suportar esta arquitetura de solução de web padrão, certifique-se de que os usuários primeiro:

1. Autenticar com a aplicação de camada intermediária

1. Um token de OAuth 2.0 On-Behalf-Of é obtido durante a autenticação

1. O token adquirido, em seguida, é utilizado para autenticar com o ou chamar as APIs que são utilizando o fluxo em-nome-de downstream

Para obter instruções sobre como orquestrar o fluxo em-nome-de, veja [fluxo de OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Também pode ver exemplos de código na [chamar uma API web a jusante](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Passos Seguintes

Para configurar e testar duplos Digital do Azure com o fluxo de concessão implícita OAuth 2.0, leia [configurar o Postman](./how-to-configure-postman.md).

Para saber mais sobre segurança de duplos Digital do Azure, leia [criar e gerir atribuições de funções](./security-create-manage-role-assignments.md).