---
title: Adicione uma aplicação de galeria - Azure Ative Directory [ Azure Ative Directory ] Microsoft Docs
description: Saiba como adicionar uma aplicação da galeria Azure AD às suas aplicações empresariais Azure.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73062599"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Adicione uma aplicação de galeria à sua organização Azure AD

O Azure Ative Directory (Azure AD) tem uma galeria que contém milhares de aplicações pré-integradas que estão habilitadas com um único signo da Enterprise. Este artigo descreve os passos gerais para adicionar uma aplicação da galeria à sua organização Azure AD.

> [!IMPORTANT]
> Em primeiro lugar, consulte a sua aplicação na [Lista de Tutoriais sobre como integrar aplicações SaaS com o Diretório Ativo Azure](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). É provável que encontre orientação passo a passo para adicionar e configurar a aplicação da galeria que pretende adicionar.

## <a name="add-a-gallery-application"></a>Adicione uma aplicação de galeria

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure AD, administrador de uma aplicação na cloud ou administrador de uma aplicação.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

1. No painel **azure Ative Directory,** selecione **aplicações Enterprise**.

    ![Abrir aplicações empresariais](media/add-gallery-app/open-enterprise-apps.png)


3. Para adicionar uma aplicação de galeria ao seu inquilino, selecione **Nova aplicação.**

    ![Selecione Nova aplicação para adicionar uma app de galeria ao seu inquilino](media/add-gallery-app/new-application.png)

 4. Mude para a nova experiência de pré-visualização da galeria: No banner no topo da página Adicionar uma página de **aplicação,** selecione o link que diz Clique aqui para experimentar a nova e melhorada galeria de **aplicações**.

5. O painel **browse Azure AD Gallery** abre e exibe azulejos para plataformas em nuvem, aplicações no local e aplicações em destaque. Note que as aplicações listadas na secção **de aplicações em destaque** têm ícones que indicam se suportam um único sinal federado (SSO) e o provisionamento.

    ![Procure uma aplicação por nome ou categoria](media/add-gallery-app/browse-gallery.png)

6. Percorra a galeria para obter a aplicação que pretende adicionar, ou procure a aplicação inserindo o seu nome na caixa de pesquisa. Em seguida, selecione a aplicação a partir dos resultados. (Opcional) No formulário, pode editar o nome da aplicação para corresponder às necessidades da sua organização.

    ![Mostra como adicionar uma aplicação da galeria](media/add-gallery-app/create-application.png)

7. Selecione **Criar**. É apresentada uma página de introdução com as opções para configurar a aplicação para a sua organização.

## <a name="configure-user-sign-in-properties"></a>Configurar as propriedades de início de sessão do utilizador

1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar propriedades painele](media/add-gallery-app/edit-properties.png)

1. Defina as seguintes opções para determinar como os utilizadores que estão designados ou não atribuídos à aplicação podem iniciar sessão na aplicação e se um utilizador pode ver a aplicação no painel de acesso.

    - **Ativada para os utilizadores iniciarem sessão** determina se os utilizadores atribuídos à aplicação podem iniciar sessão.
    - **A atribuição do utilizador necessária** determina se os utilizadores que não estão designados para a aplicação podem iniciar sessão.
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
       | Habilitado para os utilizadores iniciarem o seu insessão? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores não atribuídos podem iniciar sessão? | Os utilizadores não atribuídos podem ver a aplicação?* |
       | sim | sim | sim | não  | não   |
       | sim | sim | não  | não  | não   |
       | sim | não  | sim | sim | não   |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O utilizador pode ver a aplicação no painel de acesso e no iniciador de aplicações do Office 365?

1. Para utilizar um logótipo personalizado, crie um logótipo que seja de 215 por 215 pixels e guarde-o em formato PNG. Em seguida, navegue para o seu logotipo e faça o upload.

    ![Alterar o logótipo](media/add-gallery-app/change-logo.png)

1. Quando terminar, selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Agora que adicionou a aplicação à sua organização Azure AD, [escolha um único método de inscrição](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que deseja utilizar e consulte o artigo apropriado abaixo:

- [Configurar o início de sessão único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configure o sinal único da palavra-passe](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar o início de sessão ligado](configure-linked-sign-on.md)

