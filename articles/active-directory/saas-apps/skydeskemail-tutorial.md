---
title: 'Tutorial: Integração de Diretório Ativo Azure com SkyDesk Email [ SkyDesk Email ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SkyDesk Email.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: faaa5dcc435452d6ed9e0f2c5b481df1e352dfd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090436"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Integração de Diretório Ativo Azure com SkyDesk Email

Neste tutorial, aprende-se a integrar o SkyDesk Email com o Azure Ative Directory (Azure AD).
Integrar o SkyDesk Email com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao SkyDesk Email.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SkyDesk Email (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o SkyDesk Email, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por e-mail skyDesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SkyDesk Email suporta **SP** iniciado SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>Adicionar SkyDesk Email da galeria

Para configurar a integração do SkyDesk Email em Azure AD, precisa adicionar skyDesk Email da galeria à sua lista de aplicações saaS geridas.

**Para adicionar SkyDesk Email da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SkyDesk Email**, selecione **SkyDesk Email** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![SkyDesk Email na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com skyDesk Email com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SkyDesk Email.

Para configurar e testar o único sinal de Acesso Azure AD com o SkyDesk Email, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SkyDesk Email Single Sign-On](#configure-skydesk-email-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-skydesk-email-test-user)** do teste skyDesk Email - para ter uma contrapartida da Britta Simon no SkyDesk Email que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Entrada da Azure AD com o SkyDesk Email, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **skyDesk email,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![SkyDesk Email Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte do Cliente de [E-mail SkyDesk](https://www.skydesk.jp/apps/support/) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção De Configuração do **SkyDesk Email,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-skydesk-email-single-sign-on"></a>Configure SkyDesk Email Single Sign-On

1. Num navegador web diferente, inscreva-se na sua conta skyDesk E-mail como administrador.

1. No menu em cima, clique em **Configurar**, e selecione **Org**.

    ![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Clique em **Domínios** a partir do painel esquerdo.

    ![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Clique no **Domínio adicionar**.

    ![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Introduza o seu nome de Domínio e verifique o Domínio.

    ![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Clique na **autenticação SAML** a partir do painel esquerdo.

    ![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na página de diálogo de **autenticação SAML,** execute os seguintes passos:

    ![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Para utilizar a autenticação baseada em SAML, deve ter verificado o **domínio** ou a configuração url do **portal.** Pode definir o URL do portal com o nome único.

    ![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Na caixa de texto **login URL,** cola o valor do URL de **Login,** que copiou do portal Azure.

    b. Na caixa de texto **logout** URL, cola o valor do URL de **Logout,** que copiou do portal Azure.

    c. Alterar o **URL da palavra-passe** é opcional, por isso deixe-o em branco.

    d. Clique em **Obter Chave Do Ficheiro** para selecionar o seu certificado descarregado do portal Azure e, em seguida, clique em **Abrir** para carregar o certificado.

    e. Como **Algoritmo,** selecione **RSA**.

    f. Clique em **Ok** para guardar as alterações.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao SkyDesk Email.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **SkyDesk Email**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SkyDesk Email**.

    ![O link skyDesk email na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-skydesk-email-test-user"></a>Criar o utilizador do teste de email SkyDesk

Nesta secção, cria-se um utilizador chamado Britta Simon no SkyDesk Email.

Clique no Acesso ao **Utilizador** a partir do painel esquerdo no SkyDesk Email e, em seguida, introduza o seu nome de utilizador.

![Configurar um único sinal](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Se precisar de criar utilizadores a granel, tem de contactar a equipa de suporte ao [Cliente do SkyDesk](https://www.skydesk.jp/apps/support/)Email .

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SkyDesk Email no Painel de Acesso, deve ser automaticamente inscrito no SkyDesk Email para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

