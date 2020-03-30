---
title: Autorizar contas de desenvolvimento utilizando o Diretório Ativo Azure B2C
titleSuffix: Azure API Management
description: Saiba como autorizar os utilizadores utilizando o Azure Ative Directory B2C na Gestão da API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b8215cd852d54283bfc6bd47e77d7d63ee4e2582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475498"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Como autorizar as contas de programador ao utilizar o Azure Active Directory B2C na Gestão de API do Azure

## <a name="overview"></a>Descrição geral

O Azure Ative Directory B2C é uma solução de gestão de identidade na nuvem para aplicações web e móveis viradas para o consumidor. Pode usá-lo para gerir o acesso ao seu portal de desenvolvimento. Este guia mostra-lhe a configuração necessária no seu serviço de Gestão API para integrar com o Diretório Ativo Azure B2C. Para obter informações sobre o acesso ao portal de desenvolvimento utilizando o classic Azure Ative Directory, consulte como autorizar contas de desenvolvimento utilizando o [Diretório Ativo do Azure].

> [!NOTE]
> Para completar os passos deste guia, tem primeiro de ter um inquilino Azure Ative Directory B2C para criar uma candidatura. Além disso, é preciso ter políticas de inscrição e de inscrição prontas. Para mais informações, consulte a [visão geral do Azure Ative Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizar contas de desenvolvimento utilizando o Diretório Ativo Azure B2C

1. Para começar, inicie sessão no [portal Azure](https://portal.azure.com) e localize a sua instância de Gestão API.

   > [!NOTE]
   > Se ainda não criou uma instância de serviço de Gestão API, consulte [Create a API Management no][Create an API Management service instance] Get started with [Azure API Management tutorial][Get started with Azure API Management].

1. Sob **identidades.** Clique **+Adicionar** no topo.

   O painel do fornecedor de **identidade Add** aparece à direita. Escolha **o Diretório Ativo Azure B2C**.
    
   ![Adicionar AAD B2C como fornecedor de identidade][api-management-howto-add-b2c-identity-provider]

1. Copiar o **URL de redirecionamento**.

   ![Url de redirecionamento do fornecedor de identidade AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Num novo separador, aceda ao seu inquilino Azure Ative Directory B2C no portal Azure e abra a lâmina **aplicações.**

   ![Registar uma nova candidatura 1][api-management-howto-aad-b2c-portal-menu]

1. Clique no botão **Adicionar** para criar uma nova aplicação Azure Ative Directory B2C.

   ![Registe uma nova candidatura 2][api-management-howto-aad-b2c-add-button]

1. Na **lâmina de aplicação Nova,** introduza um nome para a aplicação. Escolha **Sim** em **Web App/Web API,** e escolha **Sim** sob permitir o **fluxo implícito**. Em seguida, colá-lo-redirecionamento do **URL** copiado no passo 3 na caixa de texto **URL resposta.**

   ![Registe uma nova candidatura 3][api-management-howto-aad-b2c-app-details]

1. Se estiver a utilizar o novo portal de desenvolvimento (não o portal de desenvolvimento legado), inclua o **Nome Dado,** **Apelido**e ID de Objeto do Utilizador nas alegações **da** aplicação.

    ![Afirmações da aplicação](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Clique no botão **Criar**. Quando a aplicação é criada, aparece na lâmina **aplicações.** Clique no nome da aplicação para ver os seus detalhes.

   ![Registe uma nova candidatura 4][api-management-howto-aad-b2c-app-created]

1. A partir da lâmina **Propriedades,** copie o ID da **aplicação** para a área de reparação.

   ![ID de aplicação 1][api-management-howto-aad-b2c-app-id]

1. Volte para a Gestão **aPi Adicionar** painel de fornecedor de identidade e colar o ID na caixa de texto **Id do cliente.**
    
1.  Volte para o registo da aplicação B2C, clique no botão **Teclas** e, em seguida, clique na **tecla Generate**. Clique em **Guardar** para guardar a configuração e exibir a **tecla App**. Copie a chave para a pasta.

    ![Chave da aplicação 1][api-management-howto-aad-b2c-app-key]

1.  Volte para a Gestão **aPi Adicionar** painel de fornecedor de identidade e colar a chave na caixa de texto Segredo do **Cliente.**
    
1.  Especifique o nome de domínio do inquilino Do Diretório Ativo Azure B2C em **inquilino Signin**.

1.  O campo **Authority** permite-lhe controlar o URL de login Azure AD B2C para utilizar. Detete o valor para **<your_b2c_tenant_name>.b2clogin.com**.

1. Especifique a Política de **Inscrição** e A Política de **Inscrição** das políticas de inquilinos B2C. Opcionalmente, também pode fornecer a **Política de Edição de Perfil** e a Política de Reset de **Passwords.**

1. Depois de especificar a configuração desejada, clique em **Guardar**.

    Após a salvação das alterações, os desenvolvedores poderão criar novas contas e iniciar sessão no portal de desenvolvimento utilizando o Azure Ative Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portal de desenvolvimento - adicione autenticação da conta Azure AD B2C

No portal de desenvolvimento, o iniciar sessão com a AAD B2C é possível com o **botão 'Iniciar sessão': Widget OAuth.** O widget já está incluído na página de iniciar sessão do conteúdo do portal de desenvolvimento predefinido.

Embora uma nova conta seja criada automaticamente sempre que um novo utilizador se inscreva no AAD B2C, poderá considerar adicionar o mesmo widget à página de inscrição.

O **formulário de inscrição: OAuth** widget representa um formulário usado para se inscrever na OAuth.

> [!IMPORTANT]
> É necessário [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações da AAD entrem em vigor.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Portal de desenvolvimento legado - como se inscrever com Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Para se inscrever numa conta de desenvolvedor usando o Azure Ative Directory B2C, abra uma nova janela de navegador e vá para o portal de desenvolvimento. Clique no botão **'Inscrever-se'.**

   ![Portal de desenvolvimento 1][api-management-howto-aad-b2c-dev-portal]

2. Opte por inscrever-se no **Azure Ative Directory B2C**.

   ![Portal de desenvolvimento 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. É redirecionado para a política de inscrição configurada na secção anterior. Opte por se inscrever utilizando o seu endereço de e-mail ou uma das suas contas sociais existentes.

   > [!NOTE]
   > Se o Azure Ative Directory B2C for a única opção que está ativada no separador **Identidades** no portal da editora, será redirecionado diretamente para a política de inscrição.

   ![Portal do programador][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Quando a inscrição estiver completa, é redirecionado de volta para o portal de desenvolvimento. Está agora inscrito no portal de desenvolvimento para a sua instância de serviço de Gestão API.

    ![Inscrição completa][api-management-registration-complete]

## <a name="next-steps"></a>Passos seguintes

*  [Visão geral do Diretório Ativo Azure B2C]
*  [Diretório Ativo Azure B2C: Quadro de política extensível]
*  [Utilize uma conta Microsoft como fornecedor de identidade no Diretório Ativo Azure B2C]
*  [Utilize uma conta Google como fornecedor de identidade no Azure Ative Directory B2C]
*  [Utilize uma conta LinkedIn como fornecedor de identidade no Diretório Ativo Azure B2C]
*  [Utilize uma conta no Facebook como fornecedor de identidade no Azure Ative Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Visão geral do Diretório Ativo Azure B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Como autorizar contas de desenvolvimento usando o Diretório Ativo Azure]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Diretório Ativo Azure B2C: Quadro de política extensível]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Utilize uma conta Microsoft como fornecedor de identidade no Diretório Ativo Azure B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Utilize uma conta Google como fornecedor de identidade no Azure Ative Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Utilize uma conta no Facebook como fornecedor de identidade no Azure Ative Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Utilize uma conta LinkedIn como fornecedor de identidade no Diretório Ativo Azure B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
