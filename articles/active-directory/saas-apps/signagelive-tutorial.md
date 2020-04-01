---
title: 'Tutorial: Integração do Diretório Ativo Azure com signagelive [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160937"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Tutorial: Integração do Diretório Ativo Azure com signagelive

Neste tutorial, aprende-se a integrar a Signagelive com o Azure Ative Directory (Azure AD).
Integrar signagelive com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Signagelive.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Signagelive (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para obter mais informações sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Signagelive, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um [julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma subscrição signon-on-enabled Signagelive.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Signagelive suporta SSO iniciado por SP.

## <a name="add-signagelive-from-the-gallery"></a>Adicione Signagelive da galeria

Para configurar a integração da Signagelive em Azure AD, primeiro adicione Signagelive da galeria à sua lista de aplicações saaS geridas.

Para adicionar Signagelive da galeria, tome os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione o ícone do **Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá a **Aplicações Empresariais**e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Signagelive**. 

     ![Signagelive na lista de resultados](common/search-new-app.png)

5. **Selecione Signagelive** a partir do painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com signagelive baseado num utilizador de teste chamado **Britta Simon**.
Para que o início de sessão seja introduzido, deve estabelecer uma ligação entre um utilizador da AD Azure e o utilizador relacionado no Signagelive.

Para configurar e testar o único sinal de Azure AD com signagelive, complete primeiro os seguintes blocos de construção:

1. [Configure](#configure-azure-ad-single-sign-on) o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
2. [Configure o signagelive único](#configure-signagelive-single-sign-on) para configurar as definições de inscrição únicas no lado da aplicação.
3. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sign-on da Azure AD com britta Simon.
4. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que Britta Simon utilize um único sinal de AD Azure.
5. [Crie um utilizador de teste Signagelive](#create-a-signagelive-test-user) para ter uma contrapartida de Britta Simon em Signagelive que esteja ligada à representação da AD Azure do utilizador.
6. [Teste um único sinal](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com signagelive, tome os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Signagelive,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. Na **seleta de uma única** caixa de diálogo de método de sinalização, selecione **SAML** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, selecione **Editar** para abrir a caixa de diálogo **de configuração SAML básica.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **de Configuração SAML Básica,** tome os seguintes passos:

    ![Signagelive Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa **de URL Sign-on,** introduza um URL que utilize o seguinte padrão:`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de inscrição real. Para obter o valor, contacte a equipa de suporte ao [Cliente Signagelive](mailto:support@signagelive.com) . Pode também consultar os padrões que são mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito. Em seguida, guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção **Set-up Signagelive,** copie os URL(s) de que necessita.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-signagelive-single-sign-on"></a>Configure Signagelive Single sign-on

Para configurar um único sinal no lado signagelive, envie o Certificado descarregado **(Raw)** e os URLs copiados do portal Azure para a equipa de [suporte Signagelive](mailto:support@signagelive.com). Asseguram-se de que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** tome os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** entre **brittaSimon.**
  
    b. No campo de nomebrittasimon@yourcompanydomain.extensiondo **Utilizador,** introduza " ". Por exemplo, neste caso, podeBrittaSimon@contoso.comentrar".

    c. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, note o valor que está visualizado na caixa password.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Signagelive.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** e, em seguida, selecione **Signagelive**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Signagelive**.

    ![O link Signagelive na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o botão **de utilizador Adicionar.** Em seguida, na caixa de diálogo **adicionar atribuição,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Na caixa de diálogo **de utilizadores e grupos,** na lista **de Utilizadores,** selecione **Britta Simon**. Em seguida, clique no botão **Selecionar** na parte inferior do ecrã.

6. Se estiver à espera de um valor de papel na afirmação do SAML, então, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, clique no botão **Selecionar** na parte inferior do ecrã.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione o botão **Atribuir.**

### <a name="create-a-signagelive-test-user"></a>Criar um utilizador de teste Signagelive

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Signagelive. Trabalhe com a equipa de [suporte Signagelive](mailto:support@signagelive.com) para adicionar os utilizadores na plataforma Signagelive. Tem de criar e ativar os utilizadores antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal MyApps.

Ao selecionar o azulejo **Signagelive** no portal MyApps, deverá ser automaticamente inscrito. Para mais informações sobre o portal MyApps, consulte [o portal MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

