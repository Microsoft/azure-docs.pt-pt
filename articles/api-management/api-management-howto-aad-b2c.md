---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory B2C-gerenciamento de API do Azure | Microsoft Docs
description: Saiba como autorizar usuários usando Azure Active Directory B2C no gerenciamento de API.
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
ms.openlocfilehash: 62657134775d21ad6aabdf8f02a1e001de0a6094
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176841"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando Azure Active Directory B2C no gerenciamento de API do Azure

## <a name="overview"></a>Visão geral

Azure Active Directory B2C é uma solução de gerenciamento de identidade de nuvem para aplicativos móveis e Web voltados para o consumidor. Você pode usá-lo para gerenciar o acesso ao portal do desenvolvedor. Este guia mostra a configuração necessária em seu serviço de gerenciamento de API para integração com o Azure Active Directory B2C. Para obter informações sobre como habilitar o acesso ao portal do desenvolvedor usando Azure Active Directory clássicas, consulte [como autorizar contas de desenvolvedor usando Azure Active Directory].

> [!NOTE]
> Para concluir as etapas deste guia, você deve primeiro ter um locatário Azure Active Directory B2C para criar um aplicativo no. Além disso, você precisa ter as políticas de inscrição e de entrada prontas. Para obter mais informações, consulte [Visão geral de Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizar contas de desenvolvedor usando Azure Active Directory B2C

1. Para começar, entre no [portal do Azure](https://portal.azure.com) e localize sua instância de gerenciamento de API.

   > [!NOTE]
   > Se você ainda não criou uma instância do serviço de gerenciamento de API, consulte [criar uma instância do serviço de gerenciamento de API][Create an API Management service instance] no [tutorial introdução ao gerenciamento de API do Azure][Get started with Azure API Management].

2. Em **identidades**. Clique em **+ Adicionar** na parte superior.

   O painel **Adicionar provedor de identidade** aparece à direita. Escolha **Azure Active Directory B2C**.
    
   ![Adicionar AAD B2C como provedor de identidade][api-management-howto-add-b2c-identity-provider]

3. Copie a **URL de redirecionamento**.

   ![URL de redirecionamento do provedor de identidade AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Em uma nova guia, acesse seu locatário Azure Active Directory B2C no portal do Azure e abra a folha **aplicativos** .

   ![Registrar um novo aplicativo 1][api-management-howto-aad-b2c-portal-menu]

5. Clique no botão **Adicionar** para criar um novo aplicativo Azure Active Directory B2C.

   ![Registrar um novo aplicativo 2][api-management-howto-aad-b2c-add-button]

6. Na folha **novo aplicativo** , insira um nome para o aplicativo. Escolha **Sim** em **aplicativo Web/API Web**e escolha **Sim** em **permitir fluxo implícito**. Em seguida, Cole a **URL de redirecionamento** copiada na etapa 3 na caixa de texto **URL de resposta** .

   ![Registrar um novo aplicativo 3][api-management-howto-aad-b2c-app-details]

7. Clique no botão **Criar**. Quando o aplicativo é criado, ele aparece na folha **aplicativos** . Clique no nome do aplicativo para ver seus detalhes.

   ![Registrar um novo aplicativo 4][api-management-howto-aad-b2c-app-created]

8. Na folha **Propriedades** , copie a **ID do aplicativo** para a área de transferência.

   ![ID do aplicativo 1][api-management-howto-aad-b2c-app-id]

9. Volte para o painel **Adicionar provedor de identidade** do gerenciamento de API e cole a ID na caixa de texto **ID do cliente** .
    
10. Volte para o registro do aplicativo B2C, clique no botão **chaves** e, em seguida, clique em **gerar chave**. Clique em **salvar** para salvar a configuração e exibir a **chave do aplicativo**. Copie a chave para a área de transferência.

    ![Chave do aplicativo 1][api-management-howto-aad-b2c-app-key]

11. Volte para o painel **Adicionar provedor de identidade** do gerenciamento de API e cole a chave na caixa de texto **segredo do cliente** .
    
12. Especifique o nome de domínio do locatário de Azure Active Directory B2C no **locatário de entrada**.

13. O campo **autoridade** permite que você controle a URL de logon do Azure ad B2C a ser usada. Defina o valor como **< your_b2c_tenant_name >. b2clogin. com**.

14. Especifique a **política de inscrição** e a **política de entrada** das políticas de locatário B2C. Opcionalmente, você também pode fornecer a política de **edição de perfil** e a **política de redefinição de senha**.

15. Depois de especificar a configuração desejada, clique em **salvar**.

    Depois que as alterações forem salvas, os desenvolvedores poderão criar novas contas e entrar no portal do desenvolvedor usando Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portal do desenvolvedor-adicionar Azure AD B2C autenticação de conta

Para habilitar a entrada com AAD B2C no portal do desenvolvedor, você precisa adicionar o widget **botões OAuth** ao formulário de entrada.

![Widget de botões do AAD](./media/api-management-howto-aad/portal-oauth-widget.png)

Embora uma nova conta seja criada automaticamente sempre que um novo usuário entrar com AAD B2C, você pode considerar adicionar o mesmo widget no formulário de inscrição.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Portal do desenvolvedor herdado-como se inscrever com Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Para se inscrever em uma conta de desenvolvedor usando Azure Active Directory B2C, abra uma nova janela do navegador e vá para o portal do desenvolvedor. Clique no botão **inscrever-se** .

   ![Portal do desenvolvedor 1][api-management-howto-aad-b2c-dev-portal]

2. Escolha se inscrever com **Azure Active Directory B2C**.

   ![Portal do desenvolvedor 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Você será redirecionado para a política de inscrição configurada na seção anterior. Opte por se inscrever usando seu endereço de email ou uma de suas contas sociais existentes.

   > [!NOTE]
   > Se Azure Active Directory B2C for a única opção habilitada na guia **identidades** no portal do editor, você será redirecionado para a política de inscrição diretamente.

   ![Portal do programador][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Quando a inscrição for concluída, você será Redirecionado de volta para o portal do desenvolvedor. Agora você está conectado ao portal do desenvolvedor da sua instância do serviço de gerenciamento de API.

    ![Registro concluído][api-management-registration-complete]

## <a name="next-steps"></a>Passos seguintes

*  [Visão geral de Azure Active Directory B2C]
*  [Azure Active Directory B2C: estrutura de política extensível]
*  [Usar um conta Microsoft como um provedor de identidade no Azure Active Directory B2C]
*  [Usar uma conta do Google como um provedor de identidade no Azure Active Directory B2C]
*  [Usar uma conta do LinkedIn como um provedor de identidade no Azure Active Directory B2C]
*  [Use uma conta do Facebook como um provedor de identidade no Azure Active Directory B2C]



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
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Visão geral de Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Como autorizar contas de desenvolvedor usando Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: estrutura de política extensível]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Usar um conta Microsoft como um provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Usar uma conta do Google como um provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Use uma conta do Facebook como um provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Usar uma conta do LinkedIn como um provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
