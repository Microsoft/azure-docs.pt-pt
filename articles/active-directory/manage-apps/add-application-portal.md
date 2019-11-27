---
title: Início rápido-adicionar um aplicativo ao seu locatário do Azure Active Directory
description: Este início rápido utiliza o portal do Azure para adicionar uma aplicação da galeria ao seu inquilino do Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420478"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Início Rápido: Adicionar uma aplicação ao seu inquilino do Azure Active Directory

O Azure Active Directory (Azure AD) tem uma galeria que contém milhares de aplicações pré-integradas. É provável que algumas das aplicações que a sua organização utiliza estão na galeria. Este início rápido utiliza o portal do Azure para adicionar uma aplicação da galeria ao seu inquilino do Azure Active Directory (Azure AD).

Após a adição de uma aplicação ao seu inquilino do Azure AD, pode:

- Gerencie o acesso do usuário ao aplicativo com uma política de acesso condicional.
- Configurar utilizadores para o início de sessão único na aplicação com as contas do Azure AD deles.

## <a name="before-you-begin"></a>Antes de começar

Para adicionar uma aplicação ao seu inquilino, precisa de:

- Uma subscrição do Azure
- Uma subscrição com início de sessão único ativado para a aplicação

Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure AD, administrador de uma aplicação na cloud ou administrador de uma aplicação.

Para testar os passos neste tutorial, recomendamos utilizar um ambiente de não produção. Se não tiver um ambiente de não produção do Azure AD, pode [obter uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Adicionar uma aplicação ao seu inquilino

Para adicionar uma aplicação da galeria ao seu inquilino do Azure AD:

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

2. No painel de **Azure Active Directory** , selecione **aplicativos empresariais**. O painel **todos os aplicativos** é aberto e exibe uma amostra aleatória dos aplicativos em seu locatário do Azure AD.

3. Para adicionar um aplicativo da galeria ao seu locatário, selecione **novo aplicativo**. 

    ![Selecione novo aplicativo para adicionar um aplicativo da galeria ao seu locatário](media/add-application-portal/new-application.png)

 4. Mude para a nova experiência de visualização da Galeria: na faixa na parte superior da **página Adicionar um aplicativo**, selecione o link que diz **clique aqui para experimentar a Galeria de aplicativos nova e aprimorada**.

5. O painel **procurar na galeria do Azure AD (visualização)** é aberto e exibe blocos para plataformas de nuvem, aplicativos locais e aplicativos em destaque. Observe que os aplicativos listados na seção **aplicativos em destaque** têm ícones que indicam se eles dão suporte ao SSO (logon único) federado e ao provisionamento.

    ![Pesquisar um aplicativo por nome ou categoria](media/add-application-portal/browse-gallery.png)

6. Você pode procurar a galeria do aplicativo que deseja adicionar ou pesquisar o aplicativo inserindo seu nome na caixa de pesquisa. Em seguida, selecione o aplicativo nos resultados. No formulário, você pode editar o nome do aplicativo para corresponder às necessidades da sua organização. Neste exemplo, alteramos o nome para **GitHub-Test**.

    ![Mostra como adicionar um aplicativo da Galeria](media/add-application-portal/create-application.png)

7. Selecione **Criar**. É apresentada uma página de introdução com as opções para configurar a aplicação para a sua organização.

Você terminou de adicionar seu aplicativo. As secções seguintes mostram-lhe como alterar o logótipo e editar outras propriedades da aplicação.

## <a name="find-your-azure-ad-tenant-application"></a>Localizar a aplicação do seu inquilino do Azure AD

Vamos supor que teve de se ausentar e que voltou agora para retomar a configuração da aplicação. A primeira coisa a fazer é localizar seu aplicativo.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.
1. No painel de **Azure Active Directory** , selecione **aplicativos empresariais**.
1. No menu suspenso **tipo de aplicativo** , selecione **todos os aplicativos**e, em seguida, selecione **aplicar**. Para saber mais sobre as opções de visualização, veja [View tenant applications](view-applications-portal.md) (Ver aplicações do inquilino).
1. Agora, pode ver uma lista com todas as aplicações no seu inquilino do Azure AD. A lista é uma amostra aleatória. Para ver mais aplicativos, selecione **Mostrar mais** uma ou mais vezes.
1. Para localizar rapidamente um aplicativo em seu locatário, insira o nome do aplicativo na caixa de pesquisa e selecione **aplicar**. Este exemplo localiza o aplicativo GitHub-Test adicionado anteriormente.

    ![Mostra como localizar um aplicativo usando a caixa de pesquisa](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Configurar as propriedades de início de sessão do utilizador

Agora que você encontrou o aplicativo, você pode abri-lo e configurar as propriedades do aplicativo.

Para editar as propriedades do aplicativo:

1. Selecione o aplicativo para abri-lo.
2. Selecione **Propriedades** para abrir o painel Propriedades para edição.

    ![Mostra as propriedades da tela de propriedades e do aplicativo editável](media/add-application-portal/edit-properties.png)

3. Dispense alguns momentos para compreender as opções de início de sessão. As opções determinam como os usuários atribuídos ou não atribuídos ao aplicativo podem entrar no aplicativo. Além disso, as opções também determinam se um usuário pode ver o aplicativo no painel de acesso.

    - **Habilitado para que os usuários entrem?** Determina se os usuários atribuídos ao aplicativo podem entrar.
    - **Atribuição de usuário necessária?** Determina se os usuários que não estão atribuídos ao aplicativo podem entrar.
    - **Visível para os usuários?** Determina se os usuários atribuídos a um aplicativo podem vê-lo no painel de acesso e no iniciador do O365.

4. Use as tabelas a seguir para ajudá-lo a escolher as melhores opções para suas necessidades.

   - Comportamento para utilizadores **atribuídos**:

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

   - Comportamento para utilizadores **não atribuídos**:

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

## <a name="use-a-custom-logo"></a>Utilizar um logótipo personalizado

Para utilizar um logótipo personalizado:

1. Crie um logótipo com 215 por 215 píxeis e guarde-o no formato PNG.
1. Como você já encontrou seu aplicativo, selecione o aplicativo.
1. No painel esquerdo, selecione **Propriedades**.
1. Atualize o logótipo.
1. Quando tiver terminado, selecione **salvar**. 

    ![Mostra como alterar o logotipo da página de propriedades do aplicativo](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > A miniatura exibida neste painel de **Propriedades** não é atualizada imediatamente. Você pode fechar e reabrir as propriedades para ver o ícone atualizado.

## <a name="next-steps"></a>Passos seguintes

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que você deseja usar e consulte o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar logon vinculado](configure-linked-sign-on.md)
