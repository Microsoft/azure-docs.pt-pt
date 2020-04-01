---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Administrador de Acesso à Internet zscaler [ Administrador de Acesso à Internet Zscaler ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Administrador de Acesso à Internet Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a149527c6e00972991bf0b18e6f7c599799a0c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161033"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Tutorial: Integração de Diretório Ativo Azure com administrador de acesso à Internet Zscaler

Neste tutorial, aprende-se a integrar o Administrador de Acesso à Internet da Zscaler com o Azure Ative Directory (Azure AD).
Integrar o Administrador de Acesso à Internet zscaler com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Administrador de Acesso à Internet Zscaler.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Administrador de Acesso à Internet zscaler (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Administrador de Acesso à Internet Zscaler, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Administrador de Acesso à Internet Zscaler

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Administrador de Acesso à Internet Zscaler suporta **IDP** iniciado SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Adicionar o Administrador de Acesso à Internet zscaler da galeria

Para configurar a integração do Administrador de Acesso à Internet Zscaler em Azure AD, você precisa adicionar O Administrador de Acesso à Internet Zscaler da galeria à sua lista de aplicações geridas saaS.

**Para adicionar o Administrador de Acesso à Internet Zscaler da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o Administrador de Acesso à **Internet Zscaler,** selecione O Administrador de Acesso à **Internet Zscaler** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Administrador de Acesso à Internet zscaler na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o Administrador de Acesso à Internet Zscaler com base num utilizador de teste chamado **Britta Simon**.
Para que o registo único funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Administrador de Acesso à Internet Zscaler.

Para configurar e testar o único sinal de Acesso à Internet do Azure AD com o Administrador de Acesso à Internet zscaler, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. Configure o administrador de acesso à **[Internet Do Zscaler Single Sign-On](#configure-zscaler-internet-access-administrator-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de teste de teste do Administrador de Acesso à **[Internet Zscaler](#create-zscaler-internet-access-administrator-test-user)** - para ter uma contrapartida da Britta Simon no Administrador de Acesso à Internet Zscaler que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Acesso à Internet do Azure AD com o Administrador de Acesso à Internet zscaler, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do Administrador de Acesso à **Internet Zscaler,** selecione **um único sinal.**

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. No **set single sign-on com** a página SAML, clique no botão **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Domínio do Administrador de Acesso à Internet zscaler e informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite um URL de acordo com o seu requisito:

    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Na caixa de texto **URL resposta,** digite um URL de acordo com o seu requisito:

    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. A aplicação zscaler Internet Access Administrator espera as afirmações do SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Atributos do Utilizador & Reclamações** na página de integração de aplicações. No **set single sign-on com a página SAML,** clique no botão **Editar** para abrir **os atributos do utilizador & diálogo de reclamações.**

    ![O link atributo](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, configure o atributo token SAML como mostrado na imagem acima e execute os seguintes passos:

    | Nome  | Atributo fonte  |
    | ---------| ------------ |
    | Função     | user.atribuídos |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](./common/new-save-attribute.png)
    
    ![image](./common/new-attribute-details.png)

    b. Da lista de **atributos Fonte,** selelct o valor do atributo.

    c. Clique em **OK**.

    d. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar o papel em Azure AD

7. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

8. Na secção De Configurar O Administrador de Acesso à **Internet Zscaler,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Configure Zscaler Internet Access Administrator Single Sign-On

1. Numa janela de navegador web diferente, inicie sessão na sua Zscaler Internet Access Admin UI.

2. Vá à **Administração > Gestão** de Administrador e execute os seguintes passos e clique em Guardar:

    ![Administration](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Administração")

    a. Verifique **ativar a autenticação SAML**.

    b. Clique no **Upload**, para fazer upload do certificado de assinatura Azure SAML que descarregou do portal Azure no **Certificado SSL Público**.

    c. Opcionalmente, para segurança adicional, adicione os detalhes do **Emitente** para verificar o Emitente da resposta SAML.

3. Na UI do Administrador, execute os seguintes passos:

    ![Administração](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Paire sobre o menu **de ativação** perto do canto inferior esquerdo.

    b. Clique em **Ativar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Administrador de Acesso à Internet Zscaler.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione O Administrador de Acesso à **Internet zscaler**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione Administrador de **Acesso à Internet Zscaler**.

    ![A ligação do Administrador de Acesso à Internet Zscaler na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Criar o utilizador de teste de teste do Administrador de Acesso à Internet Zscaler

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Zscaler Internet Access Administrator. O Acesso à Internet zscaler não suporta o provisionamento Just-In-Time para administrador SSO. É-lhe exigido que crie manualmente uma conta De Administrador.
Para obter passos sobre como criar uma conta De administrador, consulte a documentação zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Administrador de Acesso à Internet Zscaler no Painel de Acesso, deve ser automaticamente inscrito na Zscaler Internet Access Admin UI para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
