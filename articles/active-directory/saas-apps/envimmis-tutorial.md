---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Envi MMIS [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1689517042713e9a3ce75c6ada822cb3d6ff138
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73158263"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Tutorial: Integração do Diretório Ativo Azure com a Envi MMIS

Neste tutorial, aprende-se a integrar o Envi MMIS com o Azure Ative Directory (Azure AD).
Integrar o Envi MMIS com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Envi MMIS.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Envi MMIS (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Envi MMIS, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por sinal único Envi MMIS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Envi MMIS suporta **SP** e **IDP** iniciadoS SSO

## <a name="adding-envi-mmis-from-the-gallery"></a>Adicionando Envi MMIS da galeria

Para configurar a integração do Envi MMIS em Azure AD, precisa adicionar envi MMIS da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar envi MMIS da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Envi MMIS,** selecione **Envi MMIS** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Envi MMIS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com envi MMIS com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ENVI MMIS.

Para configurar e testar o único sinal de Azure AD com o Envi MMIS, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure envi MMIS Single Sign-On](#configure-envi-mmis-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Envi MMIS test user](#create-envi-mmis-test-user)** - para ter uma contrapartida de Britta Simon em Envi MMIS que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a Envi MMIS, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Envi MMIS,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set-up single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Envi MMIS Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Envi MMIS Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de [suporte ao Cliente Envi MMIS](mailto:support@ioscorp.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na secção **'Configurar Envi MMIS',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-envi-mmis-single-sign-on"></a>Configure Envi MMIS Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no seu site Envi MMIS como administrador.

2. Clique no separador **My Domain.**

    ![Configurar o botão de salvamento de sinal único](./media/envimmis-tutorial/configure1.png)

3. Clique em **Editar**.

    ![Configurar o botão de salvamento de sinal único](./media/envimmis-tutorial/configure2.png)

4. Selecione Utilize uma caixa de verificação de **autenticação remota** e, em seguida, selecione **HTTP Redirecionamento** do dropdown do **Tipo de Autenticação.**

    ![Configurar o botão de salvamento de sinal único](./media/envimmis-tutorial/configure3.png)

5. Selecione o separador **Recursos** e, em seguida, clique em **carregar metadados**.

    ![Configurar o botão de salvamento de sinal único](./media/envimmis-tutorial/configure4.png)

6. No **popup de metadados de upload,** execute os seguintes passos:

    ![Configurar o botão de salvamento de sinal único](./media/envimmis-tutorial/configure5.png)

    a. Selecione a opção **Ficheiro** a partir do **upload De** dropdown.

    b. Faça upload do ficheiro de metadados descarregado do portal Azure selecionando o ícone de **ficheiro escolha**.

    c. Clique em **OK**.

7. Depois de carregar o ficheiro de metadados descarregado, os campos serão povoados automaticamente. Clique em **Atualizar**

    ![Configurar o botão de salvamento de sinal único](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** entre **brittaSimon.**
  
    b. No campo **de nome do Utilizador,** **escreva brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Envi MMIS.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Envi MMIS**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Envi MMIS**.

    ![O link Envi MMIS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML então no diálogo **Select Role,** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-envi-mmis-test-user"></a>Criar o utilizador de teste Envi MMIS

Para permitir que os utilizadores da AD Azure inscrevam-se no Envi MMIS, devem ser aprovisionados no Envi MMIS. No caso do Envi MMIS, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Envi MMIS como administrador.

2. Clique no separador Lista de **Utilizadores.**

    ![Adicionar Empregado](./media/envimmis-tutorial/user1.png)

3. Clique no botão **Adicionar Utilizador.**

    ![Adicionar Empregado](./media/envimmis-tutorial/user2.png)

4. Na secção **Adicionar Utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/envimmis-tutorial/user3.png)

    a. Na caixa de texto Nome do **Utilizador,** digite o nome de utilizador da conta Britta Simon como **\@brittasimon contoso.com**.
    
    b. Na caixa de texto **First Name,** escreva o primeiro nome de BrittaSimon como **Britta**.

    c. Na caixa de texto **Last Name,** escreva o último nome de BrittaSimon como **Simon.**

    d. Insira o título do utilizador no **título** da caixa de texto.
    
    e. Na caixa de texto endereço de **e-mail,** digite o endereço de e-mail da conta Britta Simon como **\@brittasimon contoso.com**.

    f. Na caixa de texto **SSO User Name,** digite o nome de utilizador da conta Britta Simon como **a brittasimon\@contoso.com**.

    g. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Envi MMIS no Painel de Acesso, deverá ser automaticamente inscrito no Envi MMIS para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

