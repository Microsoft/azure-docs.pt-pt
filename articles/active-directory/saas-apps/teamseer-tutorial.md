---
title: 'Tutorial: Integração do Diretório Ativo Azure com a TeamSeer [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3e505e4823db8c9f42bbff216f30326081110dcf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67088971"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: Integração de Diretório Ativo Azure com a TeamSeer

Neste tutorial, aprende-se a integrar o TeamSeer com o Azure Ative Directory (Azure AD).
Integrar o TeamSeer com o Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a TeamSeer.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TeamSeer (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o TeamSeer, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única do TeamSeer

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* TeamSeer apoia **SP** iniciado SSO

## <a name="adding-teamseer-from-the-gallery"></a>Adicionar TeamSeer da galeria

Para configurar a integração do TeamSeer no Azure AD, precisa de adicionar teamSeer da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar TeamSeer da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TeamSeer**, selecione **TeamSeer** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![TeamSeer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a TeamSeer com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no TeamSeer.

Para configurar e testar o único sinal de Azure AD com o TeamSeer, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o TeamSeer Single Sign-On](#configure-teamseer-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create TeamSeer test user](#create-teamseer-test-user)** - para ter uma contrapartida de Britta Simon em TeamSeer que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o TeamSeer, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **TeamSeer,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![TeamSeer Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de apoio ao [Cliente TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **set Up TeamSeer,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-teamseer-single-sign-on"></a>Configure TeamSeer Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no site da sua empresa TeamSeer como administrador.

1. Vá ao **Hr Admin.**

    ![Administração hr](./media/teamseer-tutorial/ic789634.png "Administração hr")

1. Clique em **Configurar**.

    ![Configuração](./media/teamseer-tutorial/ic789635.png "Configuração")

1. Clique em Configurar os detalhes do **fornecedor SAML**.

    ![Definições SAML](./media/teamseer-tutorial/ic789636.png "Definições SAML")

1. Na secção de detalhes do fornecedor SAML, execute os seguintes passos:

    ![Definições SAML](./media/teamseer-tutorial/ic789637.png "Definições SAML")

    a. Na caixa de texto **URL,** colhe o valor URL de **Login,** que copiou do portal Azure.

    b. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de sobrercopia e, em seguida, cole-o na caixa de texto **do Certificado Público IDP.**

1. Para completar a configuração do fornecedor SAML, execute os seguintes passos:

    ![Definições SAML](./media/teamseer-tutorial/ic789638.png "Definições SAML")

    a. Nos **Endereços de E-mail**de teste, escreva o endereço de e-mail do utilizador do teste.
  
    b. Na caixa de texto **emitentes,** digite o URL emitente do prestador de serviços.
  
    c. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao TeamSeer.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **TeamSeer**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TeamSeer**.

    ![O link TeamSeer na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-teamseer-test-user"></a>Criar o utilizador de teste TeamSeer

Para permitir que os utilizadores de Anúncios Azure entrem no TeamSeer, devem ser aprovisionados no ShiftPlanning. No caso da TeamSeer, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **TeamSeer** como administrador.

1. Vá aos **Utilizadores \> de Administração de RH** e, em seguida, clique em **Executar o novo assistente de utilizador**.

    ![Administração hr](./media/teamseer-tutorial/ic789640.png "Administração hr")

1. Na secção Detalhes do **Utilizador,** execute os seguintes passos:

    ![Detalhes do utilizador](./media/teamseer-tutorial/ic789641.png "Detalhes do utilizador")

    a. Digite o **Primeiro Nome,** **Apelido,** Nome de utilizador (endereço de **e-mail)** de uma conta Azure AD válida que pretende fornecer nas caixas de texto relacionadas.
  
    b. Clique em **Seguinte**.

1. Siga as instruções no ecrã para adicionar um novo utilizador e clique em **Terminar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador TeamSeer ou APIs fornecidas pela TeamSeer para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TeamSeer no Painel de Acesso, deve ser automaticamente inscrito no TeamSeer para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
