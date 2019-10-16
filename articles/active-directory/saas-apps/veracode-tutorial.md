---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Veracode | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50056e6b201313805e7b9253e7a962e096b9df65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372982"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Veracode

Neste tutorial, você aprenderá a integrar o Veracode com o Azure Active Directory (Azure AD). Ao integrar o Veracode ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Veracode.
* Habilite seus usuários a serem conectados automaticamente ao Veracode com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Veracode.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Veracode dá suporte ao SSO iniciado pelo **IDP**
* O Veracode dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-veracode-from-the-gallery"></a>Adicionando o Veracode da Galeria

Para configurar a integração do Veracode ao Azure AD, você precisará adicionar o Veracode da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Veracode** na caixa de pesquisa.
1. Selecione **Veracode** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Configurar e testar o logon único do Azure AD para o Veracode

Configure e teste o SSO do Azure AD com o Veracode usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Veracode.

Para configurar e testar o SSO do Azure AD com o Veracode, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Veracode](#configure-veracode-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Veracode](#create-veracode-test-user)** – para ter um equivalente de B. Simon em Veracode que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Veracode** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. O aplicativo Veracode espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo Veracode espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | FirstName |User. excertoname |
    | LastName |User. sobrenome |
    | e-mail |User. mail |

1. Na seção **Configurar Veracode** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Configurar o SSO do Veracode

1. Em uma janela diferente do navegador da Web, entre no site da empresa do Veracode como um administrador.

1. No menu na parte superior, clique em **configurações**e, em seguida, clique em **administrador**.
   
    ![](./media/veracode-tutorial/ic802911.png "Administração") de administração

1. Clique na guia **SAML** .

1. Na seção **configurações de SAML da organização** , execute as seguintes etapas:

    ![](./media/veracode-tutorial/ic802912.png "Administração") de administração

    a.  Na caixa de texto **emissor** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    b. Para carregar o certificado baixado do portal do Azure, clique em **escolher arquivo**.

    c. Selecione **habilitar registro automático**.

1. Na seção **configurações de Autoregistro** , execute as seguintes etapas e, em seguida, clique em **salvar**:

    ![](./media/veracode-tutorial/ic802913.png "Administração") de administração

    a. Como **nova ativação do usuário**, selecione **nenhuma ativação necessária**.

    b. Como **atualizações de dados do usuário**, selecione **preferência Veracode dados do usuário**.

    c. Para obter **detalhes sobre o atributo SAML**, selecione o seguinte:
      * **Funções de usuário**
      * **Administrador de política**
      * **Revisor**
      * **Líder de segurança**
      * **Executivo**
      * **Emissor**
      * **Necessitam**
      * **Todos os tipos de verificação**
      * **Associações da equipe**
      * **Equipe padrão**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Veracode.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Veracode**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-veracode-test-user"></a>Criar usuário de teste do Veracode

Para permitir que os usuários do AD do Azure façam logon no Veracode, eles devem ser provisionados no Veracode. No caso do Veracode, o provisionamento é uma tarefa automatizada. Não há nenhum item de ação para você. Os usuários são criados automaticamente se necessário durante a primeira tentativa de logon único.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário Veracode ou APIs fornecidas pelo Veracode para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Veracode no painel de acesso, você deverá ser conectado automaticamente ao Veracode para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Veracode com o Azure AD](https://aad.portal.azure.com/)