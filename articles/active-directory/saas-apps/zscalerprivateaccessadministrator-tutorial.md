---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Administrador de Acesso Privado Zscaler [ Zscaler Private Access Administrator] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Administrador de Acesso Privado Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085650"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Tutorial: Integração de Diretório Sonárito Ativo Azure com administrador de acesso privado Zscaler

Neste tutorial, aprende-se a integrar o Administrador de Acesso Privado Zscaler com o Azure Ative Directory (Azure AD).
Integrar o Administrador de Acesso Privado Zscaler com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Administrador de Acesso Privado Zscaler.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Administrador de Acesso Privado Zscaler (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Administrador de Acesso Privado Zscaler, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de assinatura ativada pelo Administrador de Acesso Privado Zscaler

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Administrador de Acesso Privado Zscaler suporta **SP** e **IDP** iniciado SSO

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Adicionar o Administrador de Acesso Privado Zscaler da galeria

Para configurar a integração do Administrador de Acesso Privado Zscaler no Azure AD, você precisa adicionar Zscaler Private Access Administrator da galeria à sua lista de aplicações geridas saaS.

**Para adicionar o Administrador de Acesso Privado Zscaler da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o Administrador de **Acesso Privado Zscaler,** selecione O Administrador de **Acesso Privado Zscaler** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![Administrador de Acesso Privado Zscaler na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o Administrador de Acesso Privado Zscaler com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Administrador de Acesso Privado Zscaler.

Para configurar e testar o único sinal de Acesso Do Azure AD com o Administrador de Acesso Privado Zscaler, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o administrador de acesso privado Zscaler Single Sign-On](#configure-zscaler-private-access-administrator-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de teste do Administrador de **[Acesso Privado Zscaler](#create-zscaler-private-access-administrator-test-user)** - para ter uma contrapartida da Britta Simon no Administrador de Acesso Privado Zscaler que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Acesso À AD Azure com o Administrador de Acesso Privado Zscaler, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do Administrador de **Acesso Privado Zscaler,** selecione **um único sinal.**

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Domínio do Administrador de Acesso Privado Zscaler e informações de inscrição única dos URLs](common/idp-relay.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.private.zscaler.com/auth/sso`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **do Estado relé,** escreva um URL:`idpadminsso`

5.  Se desejar configurar a aplicação no modo iniciado **sp,** execute o seguinte passo:

    ![Domínio do Administrador de Acesso Privado Zscaler e informações de inscrição única dos URLs](common/both-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte do Administrador de [Acesso Privado Zscaler](https://help.zscaler.com/zpa-submit-ticket) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na secção De Configurar O Administrador de **Acesso Privado Zscaler,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Configure Zscaler Private Access Administrator Single Sign-On

1. Numa janela de navegador web diferente, assine o Administrador de Acesso Privado Zscaler como Administrador.

2. Em cima, clique em **Administração** e navegue para a secção **DE AUTENTICAÇÃO** clique na **configuração idp**.

    ![Administrador de Acesso Privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. No canto superior direito, clique em **Adicionar Configuração IdP**. 

    ![Administrador de Acesso Privado Zscaler addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na página **de Configuração Add IDP** efetuar os seguintes passos:
 
    ![Idpselect administrador de acesso privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Clique em **Selecionar Ficheiro** para fazer o upload do ficheiro Metadados descarregado a partir do Azure AD no campo de upload de **ficheiros idp metadados.**

    b. Lê os **metadados idp** da AD Azure e povoa todas as informações dos campos, como mostrado abaixo.

    ![Administrador de acesso privado zscaler idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selecione **Um Sinal Único como** **Administrador**.

    d. Selecione o seu domínio a partir do campo **Domínios.**
    
    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Administrador de Acesso Privado Zscaler.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione O Administrador de **Acesso Privado Zscaler**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione Administrador de **Acesso Privado Zscaler**.

    ![O link do Administrador de Acesso Privado Zscaler na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-zscaler-private-access-administrator-test-user"></a>Criar o utilizador de teste de administrador de acesso privado Zscaler

Para permitir que os utilizadores da AD Azure assinem o Administrador de Acesso Privado Zscaler, devem ser aprovisionados no Administrador de Acesso Privado Zscaler. No caso do Administrador de Acesso Privado Zscaler, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Deadministrador de Acesso Privado Zscaler como administrador.

2. Em cima, clique em **Administração** e navegue para a secção **DE AUTENTICAÇÃO** clique na **configuração idp**.

    ![Administrador de Acesso Privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Clique em **Administradores** do lado esquerdo do menu.

    ![Administrador de administrador de acesso privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. No canto superior direito, clique em **Adicionar Administrador:**

    ![Administrador de Acesso Privado Zscaler adicionar administrador](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Na página **Adicionar Administrador,** execute os seguintes passos:

    ![Administração de utilizador do Administrador de Acesso Privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Na caixa de texto **username,** introduza o e-mail do utilizador como BrittaSimon@contoso.com.

    b. Na caixa de texto **password,** digite a Palavra-passe.

    c. Na caixa de texto **Confirmar Password,** digite a Palavra-passe.

    d. Selecione **Função** como Administrador de **Acesso Privado Zscaler**.

    e. Na caixa de texto **e-mail,** introduza o e-mail do utilizador como BrittaSimon@contoso.com.

    f. Na caixa de texto **do telefone,** digite o número de telefone.

    g. Na caixa de texto **timezone,** selecione o Fuso horário.

    h. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Administrador de Acesso Privado Zscaler no Painel de Acesso, deverá ser automaticamente inscrito no Administrador de Acesso Privado Zscaler para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

