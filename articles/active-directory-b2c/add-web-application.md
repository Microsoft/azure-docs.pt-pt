---
title: Adicionar um aplicativo Web-Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar um aplicativo Web ao seu locatário Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 88ce3931d9f47b8c16251a45e54fa96b97f038e2
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71693282"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um aplicativo de API Web ao seu locatário Azure Active Directory B2C

 Registre recursos da API Web em seu locatário para que eles possam aceitar e responder a solicitações por aplicativos cliente que apresentem um token de acesso. Este artigo mostra como registrar o aplicativo no Azure Active Directory B2C (Azure AD B2C).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **incluir aplicativo Web/API Web** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Em seu aplicativo de produção, você pode definir a URL de resposta para um valor `https://localhost:44332`como. Para fins de teste, defina a URL de `https://jwt.ms`resposta como.
8. Para **URI da ID do aplicativo**, insira o identificador usado para sua API Web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões de aplicativo para a API. Por exemplo, no [tutorial: Registrar um aplicativo no Azure Active Directory B2C](tutorial-register-applications.md), um aplicativo Web é criado em Azure ad B2C chamado *webapp1*. Você pode usar esse aplicativo para chamar a API da Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Seu aplicativo está registrado para chamar a API Web protegida. Um usuário é autenticado com Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.
