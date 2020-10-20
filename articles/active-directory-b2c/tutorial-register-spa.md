---
title: 'Tutorial: Registar uma aplicação de uma página'
titleSuffix: Azure AD B2C
description: Siga este tutorial para saber como registar uma aplicação de uma página única (SPA) em Azure Ative Directory B2C utilizando o portal Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9debcca5e589038467c8201274471e3c2698cad6
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92223086"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Tutorial: Registar uma aplicação de uma página (SPA) no Azure Ative Directory B2C

Antes de as suas [candidaturas](application-types.md) poderem interagir com o Azure Ative Directory B2C (Azure AD B2C), devem estar registadas num inquilino que gere. Este tutorial mostra-lhe como registar uma aplicação de uma página ("SPA") utilizando o portal Azure.

## <a name="overview-of-authentication-options"></a>Visão geral das opções de autenticação

Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente ("SPAs"). Os desenvolvedores escrevem-nos utilizando o JavaScript ou uma estrutura SPA como Angular, Vue e React. Estas aplicações são executadas num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor.

O Azure AD B2C oferece **duas** opções para permitir que aplicações de uma página única assinem nos utilizadores e obter fichas para aceder a serviços de back-end ou APIs web:

### <a name="authorization-code-flow-with-pkce"></a>Fluxo de código de autorização (com PKCE)
- [OAuth 2.0 Fluxo de código de autorização (com PKCE)](./authorization-code-flow.md). O fluxo de código de autorização permite que a aplicação troque um código de autorização para fichas **de identificação** para representar o utilizador autenticado e fichas de **acesso** necessárias para chamar APIs protegidas. Além disso, devolve tokens **Refresh** que fornecem acesso a longo prazo aos recursos em nome dos utilizadores sem necessidade de interação com esses utilizadores. 

Esta é a abordagem **recomendada.** Ter tokens de atualização de vida limitada ajuda a sua aplicação a adaptar-se às [limitações modernas](../active-directory/develop/reference-third-party-cookies-spas.md)de privacidade de cookies do navegador , como o Safari ITP.

Para tirar partido deste fluxo, a sua aplicação pode utilizar uma biblioteca de autenticação que o suporte, como [MSAL.js 2.x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Aplicações de uma só página-auth](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Fluxo de subvenção implícito
- [OAuth 2.0 fluxo implícito](implicit-flow-single-page-application.md). Alguns quadros, como [MSAL.js 1.x,](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)apenas apoiam o fluxo implícito de subvenções. O fluxo de subvenção implícita permite que a aplicação obtenha **ID** e tokens de **acesso.** Ao contrário do fluxo de código de autorização, o fluxo de subvenção implícita não devolve um **token Refresh**. 

![Aplicações de uma página única implícitas](./media/tutorial-single-page-app/spa-app.svg)

Este fluxo de autenticação não inclui cenários de aplicação que utilizem quadros javaScript de plataforma cruzada, tais como Electrn e React-Native. Estes cenários requerem mais capacidades de interação com as plataformas nativas.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se ainda não criou o seu próprio [Inquilino Azure AD B2C,](tutorial-create-tenant.md)crie agora um. Você pode usar um inquilino Azure AD B2C existente.

## <a name="register-the-spa-application"></a>Registar a aplicação SPA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**e, em seguida, selecione Novo **registo**.
1. Insira um **Nome** para a inscrição. Por exemplo, *spaapp1*.
1. Nos **tipos de conta suportados,** selecione Contas em qualquer fornecedor de identidade ou **diretório organizacional (para autenticar utilizadores com fluxos de utilizador)**
1. Em **Redirecionar URI,** selecione **aplicação de página única (SPA)** e, em seguida, introduza `https://jwt.ms` na caixa de texto URL.

    O redirect URI é o ponto final para o qual o utilizador é enviado pelo servidor de autorização (Azure AD B2C, neste caso) após completar a sua interação com o utilizador, e para o qual é enviado um token de acesso ou código de autorização mediante autorização bem sucedida. Numa aplicação de produção, é tipicamente um ponto final acessível ao público onde a sua aplicação está a funcionar, como `https://contoso.com/auth-response` . Para testes como este tutorial, pode `https://jwt.ms` defini-lo para , uma aplicação web de propriedade da Microsoft que exibe o conteúdo descodificado de um símbolo (o conteúdo do token nunca sai do seu navegador). Durante o desenvolvimento da aplicação, pode adicionar o ponto final onde a sua aplicação ouve localmente, como `https://localhost:5000` . Pode adicionar e modificar URIs redirecionando as suas aplicações registadas a qualquer momento.

    Aplicam-se as seguintes restrições ao redirecionamento de IUR:

    * A URL de resposta deve começar pelo regime `https` .
    * A URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do caminho URL da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu `.../abc/response-oidc` caminho, não especifique `.../ABC/response-oidc` no URL de resposta. Como o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL desajustado do `.../ABC/response-oidc` caso.

1. Em **Permissões**, selecione o *consentimento de administração grant para abrir e offline_access caixa de verificação de permissões.*
1. Selecione **Registar**.


## <a name="enable-the-implicit-flow"></a>Ativar o fluxo implícito
Se utilizar o fluxo implícito, tem de ativar o fluxo implícito de subvenção no registo da aplicação.

1. No menu esquerdo, em **Manage,** **selecione Autenticação.**
1. Sob **a concessão implícita,** selecione as caixas de verificação **de fichas de acesso** e **id.**
1. Selecione **Guardar**.

## <a name="migrate-from-the-implicit-flow"></a>Migrar do fluxo implícito

Se tiver uma aplicação existente que utilize o fluxo implícito, recomendamos migrar para utilizar o fluxo de código de autorização utilizando uma estrutura que o suporte, como [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Quando todas as aplicações de uma página única de produção representadas por um registo de aplicações estiverem a utilizar o fluxo de código de autorização, desative as definições implícitas do fluxo de subvenção. 

1. No menu esquerdo, em **Manage,** **selecione Autenticação.**
1. Sob **a concessão implícita,** desescoda as caixas de verificação **de tokens de acesso** e **iD.**
1. Selecione **Guardar**.

As aplicações que utilizam o fluxo implícito podem continuar a funcionar se deixar o fluxo implícito ativado (verificado).

* * *

## <a name="next-steps"></a>Passos seguintes

Em seguida, aprenda a criar fluxos de utilizador para permitir que os seus utilizadores se inscrevam, inscrevam-se e gerem os seus perfis.

> [!div class="nextstepaction"]
> [Criar fluxos de utilizadores em Azure Ative Directory B2C >](tutorial-create-user-flows.md)
