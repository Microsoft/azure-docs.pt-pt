---
title: 'Tutorial: Integração de Diretórios Ativos Azure com Rally Software [ Software De Rali ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093180"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integração de Diretório Ativo Azure com Software de Rali

Neste tutorial, aprende-se a integrar o Rally Software com o Azure Ative Directory (Azure AD).
Integrar o Software de Rali com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Rally Software.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Rally Software (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Rally Software, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única do Rally Software

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Rally Software suporta **SP** iniciado SSO

## <a name="adding-rally-software-from-the-gallery"></a>Adicionar Software de Rali da galeria

Para configurar a integração do Rally Software em Azure AD, precisa adicionar software de rali da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Software Rally da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **rally software**, selecione **Rally Software** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Software de Rali na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure ad com o Rally Software com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Rally Software.

Para configurar e testar o único sign-on azure ad com o Rally Software, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o rally software single sign-on](#configure-rally-software-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Rally Software test user](#create-rally-software-test-user)** - para ter uma contrapartida de Britta Simon no Rally Software que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o Rally Software, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Rally Software,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Rally Software Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<tenant-name>.rally.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<tenant-name>.rally.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente do Rally Software](https://help.rallydev.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção De Configuração de **Software de Rali,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-rally-software-single-sign-on"></a>Configure Rally Software Single Sign-On

1. Inscreva-se no seu inquilino de **Rally Software.**

2. Na barra de ferramentas na parte superior, clique em **Configurar**, e, em seguida, **selecione Subscrição**.
   
    ![Assinatura](./media/rally-software-tutorial/ic769531.png "Subscrição")

3. Clique no botão **Ação.** **Selecione Editar Subscrição** no lado superior direito da barra de ferramentas.

4. Na página de diálogo **de subscrição,** execute os seguintes passos e, em seguida, clique em **Guardar & Fechar:**
   
    ![Autenticação](./media/rally-software-tutorial/ic769542.png "Autenticação")
   
    a. Selecione **A autenticação Rally ou SSO** da autenticação.

    b. Na caixa de texto URL do fornecedor de **identidade,** colhe o valor do **Identificador Azure AD,** que copiou do portal Azure. 

    c. Na caixa de texto **SSO Logout,** cola o valor do URL de **Logout,** que copiou do portal Azure.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Rally Software.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Rally Software**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rally Software**.

    ![O link de Software de Rali na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-rally-software-test-user"></a>Criar o utilizador de teste de Rally Software

Para que os utilizadores de Anúncios Azure possam iniciar o seu acesso, devem ser aprovisionados na aplicação Rally Software utilizando os seus nomes de utilizadores do Diretório Ativo Azure.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino de Rally Software.

2. Vá ao ** \> Configuração DE UTILIZADORES**, e depois clique em + Adicionar **Novo**.
   
    ![Utilizadores](./media/rally-software-tutorial/ic781039.png "Utilizadores")

3. Digite o nome na caixa de texto do Novo Utilizador e, em seguida, clique em **Adicionar com Detalhes**.

4. Na secção **Criar utilizador,** execute os seguintes passos:
   
    ![Criar o Utilizador](./media/rally-software-tutorial/ic781040.png "Criar Utilizador")

    a. Na caixa de texto Nome do **Utilizador,** digite o nome do utilizador como **Brittsimon**.
   
    b. Na caixa de texto **endereço de e-mail,** introduza o e-mail do utilizador como brittasimon@contoso.com.

    c. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como **Britta**.

    d. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

    e. Clique em **Save & Close** (Guardar e Fechar).

   >[!NOTE]
   >Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Rally Software ou APIs fornecidas pelo Rally Software para fornecer contas de utilizadores da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Rally Software no Painel de Acesso, deve ser automaticamente inscrito no Software de Rali para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

