---
title: 'Tutorial: Integração de Diretório Ativo Azure com firmplay - Advocacia de Empregados para Recrutamento / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FirmPlay - Advocacia dos Empregados para Recrutamento.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2bdc3a13582f079cc9325ef1c6949c3ae10138eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102529"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Tutorial: Integração de Diretório Ativo Azure com firmplay - Advocacia de Empregados para Recrutamento

Neste tutorial, aprende-se a integrar o FirmPlay - Advocacia dos Colaboradores para o Recrutamento com o Diretório Ativo Azure (Azure AD).
Integrando firmplay - A advocacia dos colaboradores para o recrutamento com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a FirmPlay - Advocacia de Empregados para Recrutamento.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na FirmPlay - Advocacia dos Empregados para Recrutamento (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com firmPlay - Advocacia dos Colaboradores para Recrutamento, você precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* FirmPlay - Advocacia dos Empregados para recrutar uma única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* FirmPlay - Advocacia de Colaboradores para Recrutamento apoia **SP** iniciado SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Adicionar FirmPlay - Advocacia dos Empregados para Recrutamento a partir da galeria

Para configurar a integração da FirmPlay - Advocacia de Empregados para Recrutamento em Anúncio salô azul, você precisa adicionar FirmPlay - Advocacia de Empregados para Recrutamento da galeria para a sua lista de aplicações geridas saaS.

**Para adicionar FirmPlay - Advocacia dos Empregados para Recrutamento a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **FirmPlay - Employee Advocacy for Recruiting**, selecione **FirmPlay - Employee Advocacy for Recruiting** from result panel e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![FirmPlay - Advocacia dos Colaboradores para Recrutamento na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure AD com firmPlay - Advocacia de Empregados para Recrutamento com base num utilizador de teste chamado **Britta Simon**.
Para um único início de inscrição para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no FirmPlay - Advocacia dos Empregados para o Recrutamento.

Para configurar e testar o único sign-on da Azure AD com a FirmPlay - Advocacia dos Empregados para Recrutamento, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure FirmPlay - Advocacia dos Empregados para recrutar um único sign-on](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create FirmPlay - Employee Advocacy for Recruiting test user](#create-firmplay---employee-advocacy-for-recruiting-test-user)** - para ter uma contrapartida de Britta Simon em FirmPlay - Advocacia de Empregados para Recrutamento que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o single sign-on da Azure AD com a FirmPlay - Advocacia dos Empregados para Recrutamento, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na **página FirmPlay - Employee Advocacy for Recruiting** Application integration, selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![FirmPlay - Advocacia dos Empregados para Recrutamento de Domínio e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [firmPlay - Advocacia de Empregados para recrutar equipa](mailto:engineering@firmplay.com) de apoio ao cliente para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na **secção set up FirmPlay - Employee Advocacy for Recruiting,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Configure FirmPlay - Advocacia dos Empregados para Recrutar Um Único Sign-On

Para configurar um único sign-on no **FirmPlay - Advocacia dos Empregados para o Lado** de Recrutamento, você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [FirmPlay - Employee Advocacy for Recruiting support team](mailto:engineering@firmplay.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à FirmPlay - Advocacia dos Empregados para Recrutamento.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **FirmPlay - Advocacia de Empregados para Recrutamento**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **FirmPlay - Advocacia dos Empregados para Recrutamento**.

    ![The FirmPlay - Advocacia de Empregados para O Link de Recrutamento na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Criar FirmPlay - Advocacia dos Empregados para O Utilizador de Teste de Recrutamento

Nesta secção, cria-se uma utilizadora chamada Britta Simon no FirmPlay - Advocacia de Empregados para Recrutamento. Trabalhar com [firmPlay - Employee Advocacy for Recruiting support team](mailto:engineering@firmplay.com) para adicionar os utilizadores na plataforma FirmPlay - Employee Advocacy for Recruiting. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Ao clicar no FirmPlay - Advocacia dos Empregados para Recrutar azulejos no Painel de Acesso, deve ser automaticamente inscrito no FirmPlay - Advocacia dos Empregados para Recrutamento para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

