---
title: Autorizar contas de programador com o Azure Active Directory B2C - gestão de API do Azure | Documentos da Microsoft
description: Saiba como autorizar os utilizadores com o Azure Active Directory B2C na gestão de API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: bdc479c6cc91c3ebba343a8423a73fb2e0404a73
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992707"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Como autorizar contas de programador com o Azure Active Directory B2C na gestão de API do Azure

## <a name="overview"></a>Descrição geral

O Azure Active Directory B2C é uma solução de gestão de identidades de cloud para aplicações móveis e web voltadas para consumidores. Pode usá-lo para gerir o acesso ao seu portal do programador. Este guia mostra-lhe a configuração necessária no seu serviço de gestão de API para integrar com o Azure Active Directory B2C. Para obter informações sobre como ativar o acesso ao portal do programador com clássico do Azure Active Directory, consulte [como autorizar contas de programador através do Azure Active Directory].

> [!NOTE]
> Para concluir os passos neste guia, primeiro tem de ter um inquilino do Azure Active Directory B2C para criar uma aplicação no. Além disso, tem de ter políticas de inscrição e início de sessão prontas. Para obter mais informações, consulte [descrição geral do Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizar contas de programador com o Azure Active Directory B2C

1. Para começar, inicie sessão para o [portal do Azure](https://portal.azure.com) e localize a instância de gestão de API.

   > [!NOTE]
   > Se ainda não criou uma instância de serviço de gestão de API, veja [criar uma instância de serviço de gestão de API] [ Create an API Management service instance] no [introdução ao tutorial de gestão de API do Azure] [Get started with Azure API Management].

2. Sob **identidades**. Clique em **+ adicionar** na parte superior.

   O **fornecedor de identidade de adicionar** é apresentado o painel à direita. Escolher **do Azure Active Directory B2C**.
    
   ![Adicionar o AAD B2C como fornecedor de identidade][api-management-howto-add-b2c-identity-provider]

3. Copiar o **URL de redirecionamento**.

  ![URL de redirecionamento do fornecedor de identidade do AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Num novo separador, aceder ao seu inquilino do Azure Active Directory B2C no portal do Azure e abra o **aplicativos** painel.

  ![Registar uma nova aplicação 1][api-management-howto-aad-b2c-portal-menu]

5. Clique nas **adicionar** botão para criar uma nova aplicação do Azure Active Directory B2C.

  ![Registar uma nova aplicação 2][api-management-howto-aad-b2c-add-button]

6. Na **nova aplicação** painel, introduza um nome para a aplicação. Escolher **Sim** sob **API da Web/aplicação Web**e escolha **Sim** sob **permitir fluxo implícito**. Em seguida, cole a **URL de redirecionamento** copiou no passo 3 para o **URL de resposta** caixa de texto.

  ![Registar uma nova aplicação 3][api-management-howto-aad-b2c-app-details]

7. Clique no botão **Criar**. Quando o aplicativo é criado, aparece no **aplicativos** painel. Clique no nome de aplicação para ver os respetivos detalhes.

  ![Registar uma nova aplicação 4][api-management-howto-aad-b2c-app-created]

8. Do **propriedades** painel, copie a **ID de aplicação** na área de transferência.

  ![ID da aplicação 1][api-management-howto-aad-b2c-app-id]

9. Volte para a gestão de API **Adicionar fornecedor de identidade** painel e colar o ID para o **Id de cliente** caixa de texto.

  ![ID da aplicação 2][api-management-howto-aad-b2c-client-id]

10. Mude novamente para o registo de aplicações B2C, clique nas **chaves** botão e, em seguida, clique em **gerar chave**. Clique em **salvar** para guardar a configuração e exibir o **chave da aplicação**. Copie a chave para a área de transferência.

  ![Chave da aplicação 1][api-management-howto-aad-b2c-app-key]

11. Volte para a gestão de API **Adicionar fornecedor de identidade** painel e cole a chave no **segredo do cliente** caixa de texto.

  ![Chave da aplicação 2][api-management-howto-aad-b2c-client-secret]

12. Especifique o nome de domínio do inquilino no Azure Active Directory B2C **inquilino permitido**.

  ![Inquilino permitido][api-management-howto-aad-b2c-allowed-tenant]

13. Especifique a **política de inscrição** e **política de início de sessão** das políticas de inquilino do B2C. Opcionalmente, também pode fornecer a **política de edição de perfil** e **política de reposição de palavra-passe**.

  ![Políticas][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Para obter mais informações sobre políticas, consulte [Azure Active Directory B2C: Estrutura de política extensível].

14. Depois de especificar a configuração pretendida, clique em **guardar**.

  Depois das alterações são guardadas, os desenvolvedores poderão criar novas contas e inicie sessão no portal do programador com o Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Inscreva-se para uma conta de programador com o Azure Active Directory B2C

1. Para se inscrever para uma conta de programador com o Azure Active Directory B2C, abra uma nova janela do browser e aceda ao portal do programador. Clique nas **Inscreva-se** botão.

   ![Portal do programador 1][api-management-howto-aad-b2c-dev-portal]

2. Optar por inscrever-se com **do Azure Active Directory B2C**.

   ![Portal do programador 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Está redirecionado para a política de inscrição que configurou na secção anterior. Opte por inscrever-se com o seu endereço de e-mail ou uma das suas contas de redes sociais existentes.

   > [!NOTE]
   > Se o Azure Active Directory B2C é a única opção que está ativada no **identidades** separador no portal do publicador, será redirecionado para a política de inscrição diretamente.

   ![Portal do programador][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Quando a inscrição estiver concluída, é redirecionado para o portal do programador. Agora tem sessão iniciada portal do programador para a sua instância do serviço de gestão de API.

    ![Registo concluído][api-management-registration-complete]

## <a name="next-steps"></a>Passos Seguintes

*  [Descrição geral do Azure Active Directory B2C]
*  [Azure Active Directory B2C: Estrutura de política extensível]
*  [Utilizar uma conta Microsoft como fornecedor de identidade no Azure Active Directory B2C]
*  [Utilizar uma conta do Google como um fornecedor de identidade no Azure Active Directory B2C]
*  [Utilize uma conta do LinkedIn como um fornecedor de identidade no Azure Active Directory B2C]
*  [Utilize uma conta do Facebook como provedor de identidade no Azure Active Directory B2C]



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

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
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
[Descrição geral do Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Como autorizar contas de programador através do Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Estrutura de política extensível]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Utilizar uma conta Microsoft como fornecedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Utilizar uma conta do Google como um fornecedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Utilize uma conta do Facebook como provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Utilize uma conta do LinkedIn como um fornecedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
