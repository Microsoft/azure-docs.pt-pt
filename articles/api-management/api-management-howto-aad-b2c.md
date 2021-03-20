---
title: Autorizar contas de programadores utilizando o Azure Ative Directory B2C
titleSuffix: Azure API Management
description: Saiba como autorizar os utilizadores utilizando o Azure Ative Directory B2C em Gestão API.
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
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013569"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Como autorizar as contas de programador ao utilizar o Azure Active Directory B2C na Gestão de API do Azure

## <a name="overview"></a>Descrição geral

O Azure Ative Directory B2C é uma solução de gestão de identidade em nuvem para aplicações web e móveis viradas para o consumidor. Pode usá-lo para gerir o acesso ao seu portal de desenvolvedores. Este guia mostra-lhe a configuração necessária no seu serviço de Gestão API para integrar com o Azure Ative Directory B2C. Para obter informações sobre como permitir o acesso ao portal do desenvolvedor utilizando o clássico Azure Ative Directory, consulte [Como autorizar contas de programadores utilizando o Azure Ative Directory].

> [!NOTE]
> Para completar os passos neste guia, você deve primeiro ter um inquilino Azure Ative Diretório B2C para criar uma aplicação em. Além disso, precisa de ter as políticas de inscrição e inscrição prontas. Para mais informações, consulte [a visão geral do Azure Ative Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizar contas de programadores utilizando o Azure Ative Directory B2C

1. Para começar, inicie seduca no [portal Azure](https://portal.azure.com) e localize a sua instância de Gestão API.

   > [!NOTE]
   > Se ainda não criou uma instância de serviço de Gestão da API, consulte Criar uma instância de [serviço de Gestão da API][Create an API Management service instance] no [Tutorial de Gestão da API da Azure.][Get started with Azure API Management]

1. Sob **identidades.** Clique **em +Adicionar** na parte superior.

   O **painel de fornecedor de identidade Add** aparece à direita. Escolha **O Azure Ative Directory B2C**.
    
   ![Adicionar AAD B2C como fornecedor de identidade][api-management-howto-add-b2c-identity-provider]

1. Copie o **URL de redirecionamento**.

   ![URL de redirecionamento de fornecedor de identidade AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Num novo separador, aceda ao seu inquilino Azure Ative Directory B2C no portal Azure e abra a lâmina **aplicações.**

   ![Registar uma nova aplicação 1][api-management-howto-aad-b2c-portal-menu]

1. Clique no botão **Adicionar** para criar uma nova aplicação Azure Ative Directory B2C.

   ![Registar uma nova aplicação 2][api-management-howto-aad-b2c-add-button]

1. Na lâmina de **aplicação Nova,** insira um nome para a aplicação. Escolha **Sim** na **Web App/Web API,** e escolha **Sim** em Permitir o **fluxo implícito.** Em seguida, cole o **URL de redirecionamento** copiado no passo 3 na caixa de texto **URL de resposta.**

   ![Registar uma nova aplicação 3][api-management-howto-aad-b2c-app-details]

1. Se estiver a utilizar o novo portal de desenvolvedores (não o portal de desenvolvimento legado), inclua o **Nome Dado,** **Apelido** e **ID de Objeto do Utilizador** nas reclamações da aplicação.

    ![Afirmações da aplicação](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Clique no botão **Criar**. Quando a aplicação é criada, aparece na lâmina **aplicações.** Clique no nome da aplicação para ver os seus detalhes.

   ![Registar uma nova aplicação 4][api-management-howto-aad-b2c-app-created]

1. A partir da lâmina **Propriedades,** copie o **ID da aplicação** para a área de transferência.

   ![ID de aplicação 1][api-management-howto-aad-b2c-app-id]

1. Volte para o painel de **fornecedor de identidade** API Add e cole o ID na caixa de texto do **Cliente.**
    
1.  Volte para o registo da aplicação B2C, clique no botão **Chaves** e, em seguida, clique na **tecla Gerar**. Clique em **Guardar** para guardar a configuração e exibir a **tecla App**. Copie a chave da pasta.

    ![Chave da aplicação 1][api-management-howto-aad-b2c-app-key]

1.  Volte para o painel de **fornecedor de identidade** API Add e cole a chave na caixa de texto Do Cliente **Secreto.**
    
1.  Especificar o nome de domínio do inquilino B2C do Azure Ative Directory B2C no **inquilino de Signin.**

1.  O campo **Autoridade** permite-lhe controlar o URL de login Azure AD B2C para usar. Desa esta qual é o valor **para<your_b2c_tenant_name>.b2clogin.com**.

1. Especifique a **Política de Inscrição** e Política de **Inscrição** das políticas de Inquilino B2C. Opcionalmente, também pode fornecer a **Política de Edição de Perfis** e a Política de Reset de **Password.**

1. Depois de ter especificado a configuração desejada, clique em **Guardar**.

    Após a economização das alterações, os desenvolvedores poderão criar novas contas e iniciar sessão no portal do desenvolvedor utilizando o Azure Ative Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portal do desenvolvedor - adicione a autenticação da conta Azure AD B2C

No portal do desenvolvedor, o s-in com AAD B2C é possível com o **botão de iniciar sposição: widget OAuth.** O widget já está incluído na página de inscrição do conteúdo do portal do desenvolvedor predefinido.

Apesar de uma nova conta ser criada automaticamente sempre que um novo utilizador entrar no AAD B2C, poderá considerar adicionar o mesmo widget à página de inscrição.

O **formulário de inscrição: OAuth** widget representa um formulário usado para se inscrever na OAuth.

> [!IMPORTANT]
> É necessário [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações da AAD entrem em vigor.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Portal de desenvolvimento legado - como se inscrever com Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Para se inscrever numa conta de programador utilizando o Azure Ative Directory B2C, abra uma nova janela do navegador e vá ao portal do desenvolvedor. Clique no botão **Iniciar sção.**

   ![Portal do Desenvolvedor 1][api-management-howto-aad-b2c-dev-portal]

2. Opte por se inscrever no **Azure Ative Directory B2C**.

   ![Portal do desenvolvedor 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Está redirecionado para a política de inscrição que configura na secção anterior. Opte por se inscrever usando o seu endereço de e-mail ou uma das suas contas sociais existentes.

   > [!NOTE]
   > Se o Azure Ative Directory B2C for a única opção ativada no separador **Identidades** no portal da editora, será redirecionado diretamente para a política de inscrição.

   ![Portal do programador][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Quando a inscrição estiver completa, és redirecionado de volta para o portal do desenvolvedor. Está agora inscrito no portal do desenvolvedor para a sua instância de serviço de Gestão API.

    ![Inscrição completa][api-management-registration-complete]

## <a name="next-steps"></a>Passos seguintes

*  [Visão geral do Azure Ative Directory B2C]
*  [Azure Ative Directory B2C: Quadro de política extensível]
*  [Utilize uma conta Microsoft como fornecedor de identidade no Azure Ative Directory B2C]
*  [Utilize uma conta google como fornecedor de identidade no Azure Ative Directory B2C]
*  [Utilize uma conta LinkedIn como fornecedor de identidade no Azure Ative Directory B2C]
*  [Utilize uma conta do Facebook como fornecedor de identidade no Azure Ative Directory B2C]



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

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Visão geral do Azure Ative Directory B2C]: ../active-directory-b2c/overview.md
[Como autorizar contas de programadores utilizando o Azure Ative Directory]: ./api-management-howto-aad.md
[Azure Ative Directory B2C: Quadro de política extensível]: ../active-directory-b2c/user-flow-overview.md
[Utilize uma conta Microsoft como fornecedor de identidade no Azure Ative Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Utilize uma conta google como fornecedor de identidade no Azure Ative Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Utilize uma conta do Facebook como fornecedor de identidade no Azure Ative Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Utilize uma conta LinkedIn como fornecedor de identidade no Azure Ative Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
