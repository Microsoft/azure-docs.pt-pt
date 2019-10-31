---
title: Adicionar um aplicativo não Galeria-plataforma de identidade da Microsoft | Microsoft Docs
description: Adicione um aplicativo que não seja da galeria ao seu locatário do Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6656361fd4634c46cd5216b57eb8465536319f09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062770"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Adicionar um aplicativo não listado (não Galeria) à sua organização do Azure AD

Além das opções na [Galeria de aplicativos do Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), você tem a opção de adicionar um **aplicativo inexistente na Galeria**. Você pode adicionar qualquer aplicativo que já existe em sua organização ou qualquer aplicativo de terceiros de um fornecedor que ainda não faz parte da galeria do Azure AD. Dependendo do seu [contrato de licença](https://azure.microsoft.com/pricing/details/active-directory/), os seguintes recursos estão disponíveis:

- Integração de autoatendimento de qualquer aplicativo que ofereça suporte a provedores de identidade [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (iniciado pelo SP ou pelo IDP)
- Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](what-is-single-sign-on.md#password-based-sso)
- Conexão de autoatendimento de aplicativos que usam o [sistema para o protocolo scim (gerenciamento de identidade entre domínios) para provisionamento de usuário](use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links a qualquer aplicativo no [iniciador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso do Azure ad](what-is-single-sign-on.md#linked-sign-on)

Este artigo descreve como adicionar um aplicativo que não seja da galeria a **aplicativos empresariais** no portal do Azure sem escrever código. Se, em vez disso, você estiver procurando orientação para desenvolvedores sobre como integrar aplicativos personalizados ao Azure AD, consulte [cenários de autenticação do Azure ad](../develop/authentication-scenarios.md). Ao desenvolver um aplicativo que usa um protocolo moderno como [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar usuários, você pode registrá-lo com a plataforma de identidade da Microsoft usando a experiência de [registros de aplicativo](../develop/quickstart-register-app.md) no portal do Azure.

## <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na Galeria

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com/) usando sua conta de administrador da plataforma de identidade da Microsoft.

2. Selecione **aplicativos empresariais** > **novo aplicativo**.

3. (Opcional, mas recomendado) Na caixa de pesquisa **procurar na galeria do Azure ad** , insira o nome de exibição do aplicativo. 

4. Selecione **criar seu próprio aplicativo**. A página **criar seu próprio aplicativo** é exibida.

   ![Adicionar aplicação](media/add-non-gallery-app/create-your-own-application.png)

5. Comece a digitar o nome de exibição para o novo aplicativo. Se houver aplicativos de galeria com nomes semelhantes, eles aparecerão em uma lista de resultados da pesquisa.

   > [!NOTE]
   > É recomendável usar a versão da galeria do seu aplicativo sempre que possível. Se o aplicativo que você deseja adicionar aparecer nos resultados da pesquisa, selecione o aplicativo e ignore o restante deste procedimento.

6. Em **o que você está procurando fazer com seu aplicativo?** escolha **integrar qualquer outro aplicativo que você não encontre na Galeria**. Essa opção é normalmente usada para aplicativos SAML e WS-enalimentados.

   > [!NOTE]
   > As outras duas opções são usadas nos seguintes cenários:
   >* **Configurar o proxy de aplicativo para acesso remoto seguro a um aplicativo local** abre a página de configuração para conectores e proxy de aplicativo do AD do Azure.
   >* **Registrar um aplicativo no qual você está trabalhando para integrar com o Azure ad** abre a página **registros de aplicativo** . Essa opção é normalmente usada para aplicativos OpenID Connect.

7. Selecione **Criar**. A página **visão geral** do aplicativo é aberta.

## <a name="configure-user-sign-in-properties"></a>Configurar as propriedades de início de sessão do utilizador

1. Selecione **Propriedades** para abrir o painel Propriedades para edição.

    ![Editar painel de propriedades](media/add-non-gallery-app/edit-properties.png)

2. Defina as opções a seguir para determinar como os usuários atribuídos ou não atribuídos ao aplicativo poderão entrar no aplicativo e se um usuário puder ver o aplicativo no painel de acesso.

    - **Ativada para os utilizadores iniciarem sessão** determina se os utilizadores atribuídos à aplicação podem iniciar sessão.
    - **Atribuição de usuário necessária** determina se os usuários que não estão atribuídos ao aplicativo podem entrar.
    - **Visível para o utilizador** determina se os utilizadores atribuídos a uma aplicação podem vê-la no painel de acesso e no iniciador do O365.

      Comportamento para utilizadores **atribuídos**:

       | Definições da propriedades da aplicação | | | Experiência para utilizadores atribuídos | |
       |---|---|---|---|---|
       | Ativado para os utilizadores iniciarem sessão? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores atribuídos podem iniciar sessão? | Os utilizadores atribuídos podem ver a aplicação?* |
       | sim | sim | sim | sim | sim  |
       | sim | sim | não  | sim | não   |
       | sim | não  | sim | sim | sim  |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

      Comportamento para utilizadores **não atribuídos**:

       | Definições da propriedades da aplicação | | | Experiência para utilizadores não atribuídos | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores não atribuídos podem iniciar sessão? | Os utilizadores não atribuídos podem ver a aplicação?* |
       | sim | sim | sim | não  | não   |
       | sim | sim | não  | não  | não   |
       | sim | não  | sim | sim | não   |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O utilizador pode ver a aplicação no painel de acesso e no iniciador de aplicações do Office 365?

3. Para usar um logotipo personalizado, crie um logotipo que seja 215 por 215 pixels e salve-o no formato PNG. Em seguida, navegue até o logotipo e carregue-o.

    ![Alterar o logótipo](media/add-non-gallery-app/change-logo.png)

4. Quando tiver terminado, selecione **salvar**.

## <a name="next-steps"></a>Passos seguintes

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que você deseja usar e consulte o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar logon vinculado](configure-linked-sign-on.md)
