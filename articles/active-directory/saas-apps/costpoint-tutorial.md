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
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879624"
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

## <a name="adding-costpoint-from-the-gallery"></a>Adicionando o Costpoint da Galeria

Para configurar a integração do Costpoint ao Azure AD, você precisará adicionar o Costpoint da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Costpoint** na caixa de pesquisa.
1. Selecione **Costpoint** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Costpoint usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Costpoint.

Para configurar e testar o SSO do Azure AD com o Costpoint, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
2. **[Configure o Costpoint](#configure-costpoint)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
4. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Crie um usuário de teste do Costpoint](#create-costpoint-test-user)** para ter um equivalente de B. Simon em Costpoint que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Costpoint** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    > [!NOTE]
    > Você obterá o arquivo de metadados do provedor de serviços na seção **gerar metadados do Costpoint** , que é explicada posteriormente no tutorial.
 
    1. Clique em **carregamento de ficheiro de metadados**.
    
    1. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.
    
    1. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente nas caixas de texto da seção Costpoint

        > [!Note]
        > Se os valores do **identificador** e da **URL de resposta** não estiverem sendo polulated automáticos, preencha os valores manualmente de acordo com seu requisito. Verifique se o **identificador (ID da entidade)** e a **URL de resposta (URL do serviço do consumidor de asserção)** estão definidos corretamente e se a **URL do ACS** é uma URL Costpoint válida terminando com **/LoginServlet.CPS**.

    1. Clique em **definir URLs adicionais**.

    1. Na caixa de texto **estado** de retransmissão, digite um valor usando o seguinte padrão:`system=[your system], (for example, **system=DELTEKCP**)`

1. Se você quiser configurar o aplicativo no modo iniciado pelo **SP** , execute a seguinte etapa:
    
    Na caixa de texto **URL de logon** , digite uma URL:`https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e o estado de retransmissão reais. Contate a [equipe de suporte ao cliente do Costpoint](https://www.deltek.com/about/contact-us) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no ícone de cópia para copiar a URL de **metadados de Federação do aplicativo** e salve-a no bloco de notas.

   ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Gerar metadados do Costpoint

A configuração de SSO do SAML do Costpoint é explicada no guia do **DeltekCostpoint711Security. pdf** . A partir disso, consulte a **instalação de logon único do SAML-> configurar o logon único do SAML entre o Costpoint e o Azure ad** seção. Siga as instruções e gere o arquivo **XML de metadados de Federação do SP Costpoint** . Use isso na **configuração básica do SAML** no portal do Azure.

![Utilitário de configuração do Costpoint](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> Você obterá o guia **DeltekCostpoint711Security. pdf** da [equipe de suporte ao cliente do Costpoint](https://www.deltek.com/about/contact-us). Se você não tiver esse arquivo, entre em contato com eles para obter esse arquivo.

### <a name="configure-costpoint"></a>Configurar o Costpoint

Retorne **ao utilitário de configuração do Costpoint** e cole a URL de metadados de Federação do **aplicativo** na caixa de texto XML de metadados de **Federação do IDP** e continue as instruções do guia **DeltekCostpoint711Security. pdf** para concluir o Configuração do SAML do Costpoint. 

![Utilitário de configuração do Costpoint](./media/costpoint-tutorial/config01.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso B. Simon ao Costpoint.

1. Na portal do Azure, selecione **aplicativos** > empresariais**todos os aplicativos**.
1. Na lista de aplicativos, selecione **Costpoint**.
1. Na seção **gerenciar** da página Visão geral do aplicativo, selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-costpoint-test-user"></a>Criar usuário de teste do Costpoint

Nesta seção, você criará um usuário no Costpoint. Suponha que a **ID de usuário** seja **B. Simon** e o nome **b. Simon**. Trabalhe com a [equipe de suporte ao cliente do Costpoint](https://www.deltek.com/about/contact-us) para adicionar o usuário na plataforma Costpoint. O usuário deve ser criado e ativado antes de usar o logon único.
 
Depois de criada, a seleção **do método de autenticação** do usuário deve ser **Active Directory**, a caixa de seleção **logon único do SAML** deve ser selecionada e o nome de usuário de Azure Active Directory deve ser **Active Directory ou ID do certificado** (conforme mostrado abaixo).

![Usuário Costpoint](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco Costpoint no painel de acesso, você deverá entrar automaticamente no Costpoint para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)