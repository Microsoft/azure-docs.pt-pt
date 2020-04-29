---
title: 'Tutorial: Integração do Diretório Ativo Azure com o AnswerHub [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a124832bd42a0a144ebc6000b818fb825aa422ef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73152986"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração de Diretório Ativo Azure com o AnswerHub

Neste tutorial, aprende-se a integrar o AnswerHub com o Azure Ative Directory (Azure AD).
Integrar o AnswerHub com a AD Azure proporciona estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao AnswerHub.
* Pode permitir que os seus utilizadores instem automaticamente no AnswerHub com as suas contas Azure AD (única sessão).
* Pode gerir as suas contas a partir de uma localização central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o AnswerHub, precisa do seguinte:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode começar um [julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma subscrição answerHub que tem um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* AnswerHub suporta SSO iniciado por SP.

## <a name="add-answerhub-from-the-gallery"></a>Adicione o AnswerHub da galeria

Para configurar a integração do AnswerHub em Azure AD, precisa adicionar o AnswerHub da galeria às suas aplicações saaS geridas.

**Para adicionar o AnswerHub da galeria:**

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![Botão de Diretório Ativo Azure](common/select-azuread.png)

2. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.

    ![Lâmina de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela.

    ![Novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **AnswerHub**. Selecione **AnswerHub** na lista de resultados e, em seguida, selecione **Adicionar**.

     ![AnswerHub na lista de resultados](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurar e testar o single sign-on da Azure AD

Nesta secção, configura e testa um único sign-on azure com o AnswerHub utilizando um utilizador de teste chamado Britta Simon.
Para um único início de sessão, é necessário estabelecer uma ligação entre um utilizador da AD Azure e o utilizador correspondente no AnswerHub.

Para configurar e testar o único signo do Azure AD com o AnswerHub, é necessário completar estas tarefas:

1. [Configure](#configure-azure-ad-single-sign-on) o único sinal de ad da Azure para permitir que os seus utilizadores utilizem a funcionalidade.
2. [Configure](#configure-answerhub-single-sign-on) o único sinal do AnswerHub para configurar as definições de inscrição únicas no lado da aplicação.
3. [Crie um utilizador de teste azure ad](#create-an-azure-ad-test-user) chamado Britta Simon.
4. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie um utilizador de teste AnswerHub que corresponda e esteja ligado ao utilizador de teste Azure AD.
6. [Teste um único sinal](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, configura um único sinal de Acesso Azure no portal Azure.

**Para configurar o único sign-on da Azure AD com o AnswerHub:**

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **AnswerHub,** selecione **Single sign-on**.

    ![Botão de sinalização único](common/select-sso.png)

2. Na **seleta de uma** única caixa de diálogo de método de início de sinal, selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Caixa de diálogo de método seletiva única](common/select-saml-option.png)

3. Na configuração do Single Sign-On com a página **SAML,** selecione o ícone de edição para abrir a caixa de diálogo **de configuração SAML básica.**

    ![Configurar um único sign-on com a página SAML](common/edit-urls.png)

4. Na secção **de Configuração SAML Básica,** complete os seguintes passos:

    ![Secção básica de configuração SAML](common/sp-identifier.png)

    a. No **signo na** caixa URL, introduza um URL que tenha este padrão:`https://<company>.answerhub.com`

    b. Na caixa **Identifier (Id** da Entidade), introduza um URL que tenha este padrão:`https://<company>.answerhub.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e identificador de inscrição real. Contacte a equipa de suporte do [AnswerHub](mailto:success@answerhub.com) para obter os valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** selecione o link **de descarregamento** ao lado do **Certificado (Base64)**, de acordo com os seus requisitos, e guarde o certificado no seu computador.

    ![Link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção Configurar o **AnswerHub,** copie os URL ou URLs apropriados, com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

   Pode copiar estes URLs:
    - URL de Inicio de Sessão

    - Identificador Azure AD

    - Logout URL

### <a name="configure-answerhub-single-sign-on"></a>Configure AnswerHub único sign-on

Nesta secção, configura um único sinal para o AnswerHub.  

**Para configurar o signo único do AnswerHub:**

1. Numa janela diferente do navegador web, inscreva-se no site da empresa AnswerHub como administrador.

    > [!NOTE]
    > Se precisar de ajuda para configurar o AnswerHub, contacte a equipa de suporte do [AnswerHub](mailto:success@answerhub.com.).

2. Ir para a **Administração.**

3. No separador **Utilizador e Grupos,** no painel esquerdo, na secção **Definições Sociais,** selecione **Configuração SAML**.

4. No **separador IDP Config,** preencha estes passos:

    ![Separador de grupos & utilizadores](./media/answerhub-tutorial/ic785172.png "Configuração SAML")  
  
    a. Na caixa **DEURL IDP Login,** colhe o URL de **Login** que copiou do portal Azure.
  
    b. Na caixa **DEURL IDP Logout,** colhe o URL de **Logout** que copiou do portal Azure.

    c. Na caixa **idp Name Identifier Format,** introduza o valor **identificador** selecionado na secção **Atributos** do Utilizador no portal Azure.
  
    d. Selecione **Chaves e Certificados**.

5. Na secção **Chaves e Certificados,** preencha estes passos:

    ![Secção de Chaves e Certificados](./media/answerhub-tutorial/ic785173.png "Chaves e Certificados")  

    a. Abra o certificado codificado Base64 que descarregou do portal Azure no Notepad, copie o seu conteúdo e, em seguida, cole o conteúdo na caixa **IDP Public Key (x509 Format).**
  
    b. Selecione **Guardar**.

6. No **separador IDP Config,** selecione **Guardar** novamente.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se uma utilizadora de teste chamada Britta Simon no portal Azure.

**Para criar um utilizador de teste Azure AD:**

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![Selecione Diretório Ativo Azure, Utilizadores, Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do utilizador, complete estes passos.

    ![Propriedades do utilizador](common/user-properties.png)

    a. Na caixa **de nomes,** entre **brittaSimon.**
  
    b. Na caixa de **nome suster,** **introduza brittasimon\@<yourcompanydomain.extension>**.  
    Por exemplo, BrittaSimon@contoso.com.

    c. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, configura a utilizadora Britta Simon para utilizar o único sign-on do Azure AD, concedendo ao utilizador acesso ao AnswerHub.

**Para atribuir o utilizador de teste Azure AD:**

1. No portal Azure, selecione **aplicações Enterprise,** selecione **Todas as aplicações,** e, em seguida, selecione **AnswerHub**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **AnswerHub**.

    ![Lista de candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Adicionar painel de atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Utilizadores e grupos,** selecione **Britta Simon** na lista de **Utilizadores** e, em seguida, selecione o botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de um valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. 

7. Selecione o botão **Selecionar** na parte inferior do ecrã.

8. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-an-answerhub-test-user"></a>Criar um utilizador de teste AnswerHub

Para permitir que os utilizadores de Anúncios Azure assinem o AnswerHub, é necessário adicioná-los no AnswerHub. No AnswerHub, esta tarefa é feita manualmente.

**Para configurar uma conta de utilizador:**

1. Inscreva-se no site da empresa **AnswerHub** como administrador.

2. Ir para a **Administração.**

3. Selecione o separador **Utilizadores & Grupos.**

4. No painel esquerdo, na secção **Gerir utilizadores,** selecione **Criar ou importar utilizadores**, e, em seguida, selecione Utilizadores & **Grupos**.

   ![Separador de grupos & utilizadores](./media/answerhub-tutorial/ic785175.png "Grupos de & de utilizadores")

5. Nas caixas apropriadas, introduza o **endereço de e-mail,** **nome de utilizador**e **palavra-passe** de uma conta Azure AD válida que pretende adicionar e, em seguida, selecione **Guardar**.

> [!NOTE]
> Pode utilizar qualquer outra ferramenta de criação de conta de utilizador ou API fornecida pelo AnswerHub para configurar contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o painel de acesso.

Quando selecionar o azulejo AnswerHub no painel de acesso, deve ser automaticamente inscrito no AnswerHub para o qual configura o SSO. Para mais informações sobre o painel de acesso, consulte [Introdução ao painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

