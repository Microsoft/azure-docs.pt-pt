---
title: 'Tutorial: Integração do Azure Active Directory com o Costpoint | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840106"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar o Costpoint ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Costpoint com o Azure Active Directory (Azure AD). Ao integrar o Costpoint ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Costpoint.
* Habilite seus usuários a serem conectados automaticamente ao Costpoint com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Costpoint habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Costpoint dá suporte ao **SP e** ao SSO iniciado pelo IDP.

## <a name="generate-costpoint-metadata"></a>Gerar metadados do Costpoint

A configuração de SSO do SAML do Costpoint é explicada no guia do **DeltekCostpoint711Security. pdf** . Baixe este guia do site de suporte do Deltek Costpoint e consulte a **instalação do logon único do saml** > **Configurar o logon único do SAML entre Costpoint e Microsoft Azure** seção. Siga as instruções e gere um arquivo **XML de metadados de Federação do SP Costpoint** . 

![Utilitário de configuração do Costpoint](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Adicionar o Costpoint da Galeria

Para integrar o Costpoint ao Azure AD, primeiro adicione o Costpoint à sua lista de aplicativos SaaS gerenciados da galeria no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.

1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .

   ![O botão do Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicativos empresariais** > **todos os aplicativos**.

   ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.

   ![O novo botão de aplicativo](common/add-new-app.png)

1. Na seção **Adicionar da Galeria** , insira **Costpoint** na caixa de pesquisa.

   ![Costpoint na lista de resultados](common/search-new-app.png)

1. Na lista de resultados, selecione **Costpoint**e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Configurar e testar o único sgn do Azure AD

Configure e teste o SSO do Azure AD com o Costpoint usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Costpoint.

Para configurar e testar o SSO do Azure AD com o Costpoint, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
1. **[Configure o Costpoint](#configure-costpoint)** para definir as configurações de SSO do SAML no lado do aplicativo.
1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Crie um usuário de teste do Costpoint](#create-a-costpoint-test-user)** para ter um equivalente de B. Simon em Costpoint que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. Na página de integração de aplicativos do **Costpoint** , selecione **logon único**.

   ![Configurar o link de logon único](common/select-sso.png)

1. Na seção **configuração básica do SAML** , se você tiver o *arquivo de metadados do provedor de serviços*, conclua estas etapas:

   > [!NOTE]
   > Você Obtém o arquivo de metadados do provedor de serviços em [gerar metadados Costpoint](#generate-costpoint-metadata). Como usar o arquivo é explicado posteriormente no tutorial.
 
   1. Selecione o botão **carregar arquivo de metadados** e, em seguida, selecione o arquivo **XML de metadados de Federação do SP Costpoint** gerado anteriormente pelo Costpoint e, em seguida, selecione o botão **Adicionar** para carregar o arquivo.

      ![Carregar o arquivo de metadados](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Quando o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na seção Costpoint.

      > [!NOTE]
      > Se os valores do **identificador** e da **URL de resposta** não forem polulated automáticos, insira os valores manualmente de acordo com seu requisito. Verifique se o **identificador (ID da entidade)** e a **URL de resposta (URL do serviço do consumidor de asserção)** estão definidos corretamente e se a **URL do ACS** é uma URL Costpoint válida que termina com **/LoginServlet.CPS**.

   1. Selecione **definir URLs adicionais**. Para o **estado de retransmissão**, insira um valor usando o seguinte padrão: `system=[your system]` (por exemplo, **System = DELTEKCP**).

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione o ícone de **cópia** para copiar a URL de metadados de **Federação do aplicativo** e salvá-la no bloco de notas.

   ![Certificado de Assinatura de SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Configurar o Costpoint

1. Retorne ao utilitário de configuração do Costpoint. Na caixa de texto **XML de metadados de Federação do IDP** , Cole o conteúdo do arquivo de URL de metadados de Federação do *aplicativo* . 

   ![Utilitário de configuração do Costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Continue as instruções do guia **DeltekCostpoint711Security. pdf** para concluir a instalação do Costpoint SAML.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado B. Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.

   !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

1. Selecione **novo usuário**.

   ![Botão novo usuário](common/new-user.png)

1. Nas propriedades do **usuário** , conclua estas etapas:

   ![A caixa de diálogo de utilizador](common/user-properties.png)

   1. No campo **nome** , digite **B. Simon**.
   
   1. No campo **nome de usuário** , digite `b.simon\@yourcompanydomain.extension` (por exemplo, B.Simon@contoso.com).
   
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido no campo **senha** .
   
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso B. Simon ao Costpoint.

1. Na portal do Azure, selecione **aplicativos empresariais** > **todos os aplicativos**.

1. Na lista de aplicativos, selecione **Costpoint**.

1. Na seção **gerenciar** da página Visão geral do aplicativo, selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar atribuição** , selecione **usuários e grupos**.

   ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , na lista **usuários** , selecione **B. Simon**. Em seguida, escolha **selecionar**.

1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e escolha **selecionar**.

1. Na caixa de diálogo **Adicionar atribuição** , selecione **atribuir**.

### <a name="create-a-costpoint-test-user"></a>Criar um usuário de teste do Costpoint

Nesta seção, você criará um usuário no Costpoint. Suponha que a ID de usuário seja **b. Simon** e o nome do usuário seja **b. Simon**. Trabalhe com a [equipe de suporte ao cliente do Costpoint](https://www.deltek.com/about/contact-us) para adicionar o usuário na plataforma Costpoint. O usuário deve ser criado e ativado antes que possa usar o logon único.

Depois que o usuário é criado, a seleção do **método de autenticação** do usuário deve ser **Active Directory**, a caixa de seleção **logon único do SAML** deve ser selecionada e o nome de usuário de Azure Active Directory deve ser **Active Directory ou ID do certificado** (mostrado na captura de tela a seguir).

![Usuário Costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco Costpoint no painel de acesso, você deverá ser conectado automaticamente ao aplicativo Costpoint porque você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais para integrar aplicativos SaaS com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
