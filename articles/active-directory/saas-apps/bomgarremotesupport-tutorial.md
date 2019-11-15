---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o suporte remoto do BeyondTrust | Microsoft Docs'
description: Saiba como configurar o logon único entre o suporte remoto do Azure Active Directory e do BeyondTrust.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o suporte remoto do BeyondTrust

Neste tutorial, você aprenderá a integrar o suporte remoto do BeyondTrust com o Azure Active Directory (Azure AD). Ao integrar o suporte remoto do BeyondTrust ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao BeyondTrust Remote Support.
* Habilite seus usuários a serem conectados automaticamente ao BeyondTrust Remote Support com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do BeyondTrust Remote Support.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O suporte remoto do BeyondTrust dá suporte ao SSO iniciado pelo **SP**
* O suporte remoto do BeyondTrust dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Adicionando o suporte remoto do BeyondTrust da Galeria

Para configurar a integração do suporte remoto do BeyondTrust ao Azure AD, você precisará adicionar o suporte remoto do BeyondTrust da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **suporte remoto BeyondTrust** na caixa de pesquisa.
1. Selecione **suporte remoto do BeyondTrust** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Configurar e testar o logon único do Azure AD para o suporte remoto do BeyondTrust

Configure e teste o SSO do Azure AD com o suporte remoto do BeyondTrust usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no suporte remoto do BeyondTrust.

Para configurar e testar o SSO do Azure AD com o suporte remoto do BeyondTrust, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de suporte remoto do BeyondTrust](#configure-beyondtrust-remote-support-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do suporte remoto do BeyondTrust](#create-beyondtrust-remote-support-test-user)** – para ter um equivalente de B. Simon no suporte remoto do BeyondTrust que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **BeyondTrust Remote Support** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<HOSTNAME>.bomgar.com/saml`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão: `https://<HOSTNAME>.bomgar.com`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Você receberá esses valores explicados posteriormente no tutorial.

1. O aplicativo de suporte remoto BeyondTrust espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além de acima, o aplicativo de suporte remoto do BeyondTrust espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | ---------------| ----------|
    | GivenName | user.givenname |
    | EmailAddress | user.mail |
    | Nome | user.userprincipalname |
    | Nome de Utilizador | user.userprincipalname |
    | Grupos | user.groups |
    | Identificador de usuário exclusivo | user.userprincipalname |

    > [!NOTE]
    > Ao atribuir grupos do Azure AD para o aplicativo de suporte remoto BeyondTrust, a opção ' grupos retornados na declaração ' precisará ser modificada de nenhum para grupo de segurança. Os grupos serão importados para o aplicativo como suas IDs de objeto. A ID de objeto do grupo do Azure AD pode ser encontrada verificando as propriedades na interface Azure Active Directory. Isso será necessário para referenciar e atribuir grupos do Azure AD às políticas de grupo corretas.

1. Ao definir o identificador de usuário exclusivo, esse valor deve ser definido como NameID-Format: **persistent**. Exigimos que este seja um identificador persistente para identificar e associar corretamente o usuário às diretivas de grupo corretas para permissões. Clique no ícone Editar para abrir a caixa de diálogo **atributos de usuário & declarações** para editar o valor exclusivo do identificador de usuário.

1. Na seção **gerenciar declaração** , clique no **formato escolher identificador de nome** e defina o valor como **persistente** e clique em **salvar**.

    ![Atributos e declarações de usuário](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o suporte remoto do BeyondTrust** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao BeyondTrust Remote Support.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **suporte remoto do BeyondTrust**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-beyondtrust-remote-support-sso"></a>Configurar SSO de suporte remoto do BeyondTrust

1. Em uma janela diferente do navegador da Web, entre no suporte remoto do BeyondTrust como administrador.

1. Clique no menu **status** e copie o **identificador**, a **URL de resposta** e a **URL de logon** e use esses valores na seção **configuração básica do SAML** no portal do Azure.

    ![Configurar o suporte remoto do BeyondTrust](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Navegue até a interface do BeyondTrust Remote Support/login em `https://support.example.com/login` em que **support.example.com** é o nome de host primário do seu dispositivo e autentique usando suas credenciais administrativas.

1. Navegue até **usuários & segurança** > **provedores de segurança**.

1. No menu suspenso, selecione **SAML** e clique no botão **criar provedor** .

1. Na seção Configurações do provedor de identidade, há uma opção para carregar os metadados do provedor de identidade. Localize o arquivo XML de metadados que você baixou do portal do Azure e clique no botão **carregar** . A **ID da entidade**, a **URL do serviço de logon único** e o certificado serão automaticamente carregados e a associação de **protocolo** precisará ser alterada para **http post**. Consulte a captura de tela abaixo:

    ![Configurar o suporte remoto do BeyondTrust](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Criar usuário de teste do suporte remoto do BeyondTrust

Iremos definir as configurações de provisionamento do usuário aqui. Os valores usados nesta seção serão referenciados na seção **atributos de usuário & declarações** no portal do Azure. Configuramos isso para ser os valores padrão que já foram importados no momento da criação, no entanto, o valor pode ser personalizado, se necessário.

![Criando usuário](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Os atributos Groups e email não são necessários para essa implementação. Se você estiver utilizando grupos do Azure AD e atribuindo-os a políticas de grupo de suporte remoto do BeyondTrust para permissões, a ID de objeto do grupo precisará ser referenciada por meio de suas propriedades no portal do Azure e colocada na seção ' grupos disponíveis '. Depois que isso tiver sido concluído, a ID de objeto/grupo do AD agora estará disponível para atribuição a uma política de grupo para permissões.

![Criando usuário](./media/bomgarremotesupport-tutorial/config-user2.png)

![Criando usuário](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Como alternativa, uma política de grupo padrão pode ser definida no provedor de segurança SAML2. Ao definir essa opção, você atribuirá a todos os usuários que autenticam por meio do SAML as permissões especificadas na política de grupo. A política de membros gerais está incluída no suporte remoto do BeyondTrust/acesso remoto privilegiado com permissões limitadas, que podem ser usadas para testar a autenticação e atribuir usuários às políticas corretas. Os usuários não preencherão a lista de usuários do SAML2 por meio do/login > usuários & segurança até a primeira tentativa de autenticação bem-sucedida. Informações adicionais sobre políticas de grupo podem ser encontradas no seguinte link: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco de suporte remoto do BeyondTrust no painel de acesso, você deverá ser conectado automaticamente ao suporte remoto do BeyondTrust para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o suporte remoto do BeyondTrust com o Azure AD](https://aad.portal.azure.com/)
