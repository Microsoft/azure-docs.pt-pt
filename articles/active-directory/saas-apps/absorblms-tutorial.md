---
title: 'Tutorial: Integração do Diretório Ativo Azure com Absorver LMS [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e absorver o LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936de76d1117c56f5a9dec48b51f33b9afa15351
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107505"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração de Diretório Ativo Azure com Absorver LMS

Neste tutorial, aprende-se a integrar o Absorb LMS com o Azure Ative Directory (Azure AD).
Integrar o LMS absorvente com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Absorver LMS.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para absorver o LMS (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Absorb LMS, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Absorva a subscrição ativada por um sinal único lMS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Absorver LMS suporta **IDP** iniciado SSO

## <a name="adding-absorb-lms-from-the-gallery"></a>Adicionar LMS absorvente da galeria

Para configurar a integração do LMS absorvente em AD Azure, você precisa adicionar Absorver LMS da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Absorver LMS da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Absorver LMS,** selecione **Absorver LMS** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Absorva o LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com Absorvel LMS com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Absorver LMS.

Para configurar e testar o único sinal de Azure AD com absorver LMS, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure absorver o sign-on único LMS](#configure-absorb-lms-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Absorb LMS user](#create-absorb-lms-test-user)** - para ter uma contrapartida de Britta Simon em Absorver LMS que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Absorb LMS, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **LMS Absorv,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. No **set single sign-on com** a página SAML, clique no botão **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Absorva informações únicas de domínio lMS e URLs](common/idp-intiated.png)

    Se estiver a utilizar **o Absorb 5 - UI** utilize a seguinte configuração:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://company.myabsorb.com/account/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://company.myabsorb.com/account/saml`

    Se estiver a utilizar **o Absorb 5 - New Learner Experience** utilize a seguinte configuração:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte ao [Cliente LMS](https://support.absorblms.com/hc/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .**

    ![image](common/edit-attribute.png)

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na secção **Absorver LMS** , copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-absorb-lms-single-sign-on"></a>Configure absorver lMS single sign-on

1. Numa nova janela do navegador web, inscreva-se no site da empresa Absorb LMS como administrador.

2. Selecione o botão **Conta** na parte superior direita.

    ![O botão Conta](./media/absorblms-tutorial/1.png)

3. No painel da Conta, selecione **Definições do Portal**.

    ![O link de definições do portal](./media/absorblms-tutorial/2.png)

4. Selecione o separador **'Gerir as definições SSO'.**

    ![O separador Utilizadores](./media/absorblms-tutorial/managesso.png)

5. Na página **'Gerir Definições de Inscrição Única',** faça o seguinte:

    ![A página de configuração de um único sinal](./media/absorblms-tutorial/settings.png)

    a. Na caixa de texto **Name,** introduza o nome de Azure AD Marketplace SSO.

    b. Selecione **SAML** como **método**.

    c. No Notepad, abra o certificado que descarregou do portal Azure. Retire as etiquetas **---CERTIFICADO---E---END** **CERTIFICATE---.** Em seguida, na caixa **chave,** colar o restante conteúdo.

    d. Na caixa **modo,** selecione **Identity Provider Iniciado**.

    e. Na caixa **Id Property,** selecione o atributo que configuracomo identificador de utilizador em Azure AD. Por exemplo, se o identificador de *nomes* for selecionado em Azure AD, selecione **Username**.

    f. Selecione **Sha256** como tipo de **assinatura**.

    g. Na caixa URL de **Login,** colhe o URL de Acesso ao **Utilizador** da página **Propriedades** da aplicação do portal Azure.

    h. No URL de **Logout,** colhe o valor URL de **Sign-Out** que copiou da janela de **sinalização Configure** do portal Azure.

    i. Alternar **automaticamente para** **Ligar**.

6. Selecione **Guardar.**

    ![O único sso login alternar](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador**`brittasimon\@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso a LMS absorvente.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Absorver LMS**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e **selecione Absorver LMS**.

    ![O link LMS absorvente na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-absorb-lms-test-user"></a>Criar absorver o utilizador de teste LMS

Para que os utilizadores de AD Azure assinem o LMS absorvente, devem ser configurados em Absorver LMS. No caso de Absorver LMS, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Absorb LMS como administrador.

2. No painel **utilizadores,** selecione **Utilizadores**.

    ![A ligação utilizadores](./media/absorblms-tutorial/absorblms_userssub.png)

3. Selecione o separador **Utilizador.**

    ![A lista de lançamentos](./media/absorblms-tutorial/absorblms_createuser.png)

4. Na página **Adicionar Utilizador,** faça o seguinte:

    ![Página adicionar utilizador](./media/absorblms-tutorial/user.png)

    a. Na caixa **De Nome-Primeiro,** digite o primeiro nome, como **Britta**.

    b. Na caixa **Do Último Nome,** escreva o sobrenome, como **Simon.**

    c. Na caixa **username,** digite um nome completo, como **Britta Simon**.

    d. Na caixa **Password,** digite a palavra-passe do utilizador.

    e. Na caixa **Confirmar Password,** reescreva a palavra-passe.

    f. Desloque o toggle **está ativo** para **ativo**.

5. Selecione **Guardar.**

    ![O único sso login alternar](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Por predefinição, o fornecimento de utilizador não está ativado no SSO. Se o cliente quiser ativar esta funcionalidade, tem de a configurar como mencionado [nesta](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentação. Também tenha em anote que a condição do utilizador só está disponível no **Absorb 5 - New Learner Experience** com URL ACS.`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Absorver LMS no Painel de Acesso, deve ser automaticamente inscrito no LMS absorvente para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
