---
title: Adicionar um aplicativo de API Web-Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar um aplicativo de API Web ao seu locatário Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 37cb242f667190fcd29bed1b7a82ca44ba2c94e9
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641546"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um aplicativo de API Web ao seu locatário Azure Active Directory B2C

 Registre recursos da API Web em seu locatário para que eles possam aceitar e responder a solicitações por aplicativos cliente que apresentem um token de acesso. Este artigo mostra como registrar uma API da Web no Azure Active Directory B2C (Azure AD B2C).

Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **incluir aplicativo Web/API Web** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Em seu aplicativo de produção, você pode definir a URL de resposta para um valor como `https://localhost:44332`. Para fins de teste, defina a URL de resposta como `https://jwt.ms`.
8. Para **URI da ID do aplicativo**, insira o identificador usado para sua API Web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *webapi1*.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Em um aplicativo de produção, você pode definir o URI de redirecionamento como um ponto de extremidade `https://localhost:5000`. Durante o desenvolvimento ou teste, você pode defini-lo como `https://jwt.ms`, um aplicativo da Web da Microsoft que exibe o conteúdo decodificado de um token (o conteúdo do token nunca sai do seu navegador). Você pode adicionar e modificar URIs de redirecionamento em seus aplicativos registrados a qualquer momento.
1. Selecione **Registar**.
1. Registre a **ID do aplicativo (cliente)** para uso no código da sua API Web.

Se você tiver um aplicativo que implementa o fluxo de concessão implícita, por exemplo, um aplicativo de página única (SPA) baseado em JavaScript, você poderá habilitar o fluxo seguindo estas etapas:

1. Em **gerenciar**, selecione **autenticação**.
1. Selecione **experimentar a nova experiência** (se mostrado).
1. Em **concessão implícita**, marque as caixas de seleção **tokens de acesso** e **tokens de ID** .
1. Selecione **Guardar**.

* * *

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, os utilizadores da API Web podem ter acesso de leitura e escrita ou podem ter apenas acesso só de leitura. Neste tutorial, vai utilizar os âmbitos para definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões de aplicativo para a API. Por exemplo, no [tutorial: registrar um aplicativo no Azure Active Directory B2C](tutorial-register-applications.md), um aplicativo Web chamado *webapp1* é registrado em Azure ad B2C. Você pode usar esse aplicativo para chamar a API da Web.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Seu aplicativo está registrado para chamar a API Web protegida. Um usuário é autenticado com Azure AD B2C para usar o aplicativo. O aplicativo obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.
