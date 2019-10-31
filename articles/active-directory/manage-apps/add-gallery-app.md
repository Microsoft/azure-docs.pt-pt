---
title: Adicionar um aplicativo de galeria-Azure Active Directory | Microsoft Docs
description: Saiba como adicionar um aplicativo da galeria do Azure AD aos seus aplicativos empresariais do Azure.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062599"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Adicionar um aplicativo da Galeria à sua organização do Azure AD

O Azure Active Directory (AD do Azure) tem uma galeria que contém milhares de aplicativos previamente integrados que são habilitados com o logon único corporativo. Este artigo descreve as etapas gerais para adicionar um aplicativo da Galeria à sua organização do Azure AD.

> [!IMPORTANT]
> Primeiro, verifique seu aplicativo na [lista de tutoriais sobre como integrar aplicativos SaaS com Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Você provavelmente encontrará orientações passo a passo para adicionar e configurar o aplicativo da galeria que deseja adicionar.

## <a name="add-a-gallery-application"></a>Adicionar um aplicativo de galeria

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure AD, administrador de uma aplicação na cloud ou administrador de uma aplicação.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

1. No painel de **Azure Active Directory** , selecione **aplicativos empresariais**.

    ![Abrir aplicações empresariais](media/add-gallery-app/open-enterprise-apps.png)


3. Para adicionar um aplicativo da galeria ao seu locatário, selecione **novo aplicativo**.

    ![Selecione novo aplicativo para adicionar um aplicativo da galeria ao seu locatário](media/add-gallery-app/new-application.png)

 4. Mude para a nova experiência de visualização da Galeria: na faixa na parte superior da **página Adicionar um aplicativo**, selecione o link que diz **clique aqui para experimentar a Galeria de aplicativos nova e aprimorada**.

5. O painel **procurar na galeria do Azure ad** é aberto e exibe blocos para plataformas de nuvem, aplicativos locais e aplicativos em destaque. Observe que os aplicativos listados na seção **aplicativos em destaque** têm ícones que indicam se eles dão suporte ao SSO (logon único) federado e ao provisionamento.

    ![Pesquisar um aplicativo por nome ou categoria](media/add-gallery-app/browse-gallery.png)

6. Navegue pela galeria para o aplicativo que você deseja adicionar ou procure o aplicativo inserindo seu nome na caixa de pesquisa. Em seguida, selecione o aplicativo nos resultados. Adicional No formulário, você pode editar o nome do aplicativo para corresponder às necessidades da sua organização.

    ![Mostra como adicionar um aplicativo da Galeria](media/add-gallery-app/create-application.png)

7. Selecione **Criar**. É apresentada uma página de introdução com as opções para configurar a aplicação para a sua organização.

## <a name="configure-user-sign-in-properties"></a>Configurar as propriedades de início de sessão do utilizador

1. Selecione **Propriedades** para abrir o painel Propriedades para edição.

    ![Editar painel de propriedades](media/add-gallery-app/edit-properties.png)

1. Defina as opções a seguir para determinar como os usuários atribuídos ou não atribuídos ao aplicativo poderão entrar no aplicativo e se um usuário puder ver o aplicativo no painel de acesso.

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

1. Para usar um logotipo personalizado, crie um logotipo que seja 215 por 215 pixels e salve-o no formato PNG. Em seguida, navegue até o logotipo e carregue-o.

    ![Alterar o logótipo](media/add-gallery-app/change-logo.png)

1. Quando tiver terminado, selecione **salvar**.

## <a name="next-steps"></a>Passos seguintes

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que você deseja usar e consulte o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar logon vinculado](configure-linked-sign-on.md)

