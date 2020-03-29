---
title: Compreender a autenticação da API - Azure Digital Twins [ Azure Digital Twins] Microsoft Docs
description: Saiba como se conectar e autenticar com APIs usando Gémeos Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513009"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Ligar e autenticar com APIs

A Azure Digital Twins utiliza o Azure Ative Directory (Azure AD) para autenticar utilizadores e proteger aplicações. A Azure AD suporta a autenticação para uma variedade de arquiteturas modernas. Todos eles baseiam-se nos protocolos padrão da indústria OAuth 2.0 ou OpenID Connect. Além disso, os desenvolvedores podem usar a Azure AD para construir aplicações de um único inquilino e linha de negócio (LOB). Os desenvolvedores também podem usar a AD Azure para desenvolver [aplicações multiarrendatárias.](how-to-multitenant-applications.md)

Para uma visão geral do Azure AD, visite a [página de fundamentos](https://docs.microsoft.com/azure/active-directory/fundamentals/) para guias passo a passo, conceitos e quickstarts.

> [!TIP]
> Siga o [Tutorial](tutorial-facilities-setup.md) para configurar e executar uma aplicação de amostra sinuosa Azure Digital Twins.

Para integrar uma aplicação ou serviço com o Azure AD, primeiro, o programador deve registar a aplicação no Azure AD. Para obter instruções detalhadas e imagens, leia [este arranque rápido](../active-directory/develop/quickstart-register-app.md).

[Cinco cenários de candidatura primária](../active-directory/develop/v2-app-types.md) são apoiados pela Azure AD:

* Aplicação de página única (SPA): Um utilizador precisa de iniciar sessão numa aplicação de uma única página que é protegida pela Azure AD.
* Navegador web para aplicação web: Um utilizador precisa de iniciar sessão numa aplicação web que é protegida pela Azure AD.
* Aplicação nativa para web API: Uma aplicação nativa que funciona num telefone, tablet ou PC precisa de autenticar um utilizador para obter recursos de uma API web que é protegida pela Azure AD.
* Aplicação web para Web API: Uma aplicação web precisa de obter recursos de uma API web protegida pela Azure AD.
* Aplicação da daemon ou servidor para web API: Uma aplicação daemon ou uma aplicação de servidor sem UI web precisa obter recursos de uma API web protegida por Azure AD.

> [!IMPORTANT]
> A Azure Digital Twins suporta ambas as seguintes bibliotecas de autenticação:
> * A mais recente Biblioteca de Autenticação da [Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * Biblioteca de [Autenticação de Diretório Ativo Azure (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Ligue para gémeos digitais de uma API web de nível médio

Quando desenvolvedores arquiteto soluções Digital Twins, eles normalmente criam uma aplicação de nível médio ou API. A aplicação ou API chama então a API de Gémeos Digitais a jusante. Para apoiar esta arquitetura de solução web padrão, certifique-se de que os utilizadores primeiro:

1. Autenticar com a aplicação de nível médio

1. Um símbolo OAuth 2.0 Em nome de um símbolo é adquirido durante a autenticação

1. O token adquirido é então usado para autenticar ou chamar APIs que estão mais a jusante usando o fluxo on-behalf-Of

Para obter instruções sobre como orquestrar o fluxo em nome, leia [OAuth 2.0 On-Behalf-Of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Também pode ver amostras de código em [Call a ajusante web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Passos seguintes

Para configurar e testar as Gémeas Digitais Azure utilizando o fluxo de subvenção implícito OAuth 2.0, leia [o Carteiro Configure](./how-to-configure-postman.md).

Para conhecer a segurança das Gémeas Digitais Azure, leia Criar e gerir atribuições de [papéis.](./security-create-manage-role-assignments.md)
