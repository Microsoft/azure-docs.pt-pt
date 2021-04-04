---
title: 'Tutorial: Integração do Diretório Ativo Azure com o AnswerHub | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AnswerHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 284ec65955de60f41d7bb0f97bf608faba3d4b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672602"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração do Diretório Ativo Azure com o AnswerHub

Neste tutorial, aprende a integrar o AnswerHub com o Azure Ative Directory (Azure AD).
A integração do AnswerHub com a AD Azure proporciona estes benefícios:

* Pode utilizar a Azure AD para controlar quem tem acesso ao AnswerHub.
* Pode permitir que os seus utilizadores inscrevam-se automaticamente no AnswerHub com as suas contas AD Azure (súpido único).
* Pode gerir as suas contas a partir de uma localização central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o AnswerHub, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode iniciar um [período experimental de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma subscrição do AnswerHub que tem um único sinal de acesso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* O AnswerHub suporta sSO iniciado pelo SP.

## <a name="add-answerhub-from-the-gallery"></a>Adicione AnswerHub da galeria

Para configurar a integração do AnswerHub no Azure AD, precisa de adicionar o AnswerHub da galeria às aplicações geridas do SaaS.

**Para adicionar AnswerHub da galeria:**

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![Botão de diretório ativo Azure](common/select-azuread.png)

2. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.

    ![Lâmina de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela.

    ![Novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o AnswerHub**. Selecione **AnswerHub** na lista de resultados e, em seguida, **selecione Adicionar**.

    ![AnswerHub na lista de resultados](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurar e testar a Azure AD um único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o AnswerHub utilizando um utilizador de teste chamado Britta Simon.
Para um único sinal de solução, é necessário estabelecer uma ligação entre um utilizador Azure AD e o utilizador correspondente no AnswerHub.

Para configurar e testar o Azure AD com o AnswerHub, é necessário completar estas tarefas:

1. [Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on) permitir que os seus utilizadores utilizem a funcionalidade.
2. [Configure o answerHub único](#configure-answerhub-single-sign-on) para configurar as definições de inscrição única no lado da aplicação.
3. Crie um utilizador de [teste AZure AD](#create-an-azure-ad-test-user) chamado Britta Simon.
4. [Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user) para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie um utilizador de teste AnswerHub que corresponda e esteja ligado ao utilizador de teste Azure AD.
6. [Teste um único sinal para](#test-single-sign-on) verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você configura Azure AD único sinal no portal Azure.

**Para configurar o Azure AD um único sinal de inscrição com o AnswerHub:**

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **AnswerHub,** selecione **Single sign-on**.

    ![Botão de inscrição único](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de entrada.

    ![Caixa de diálogo de método de s seletor único](common/select-saml-option.png)

3. Na **configuração single Sign-On com** a página SAML, selecione o ícone de edição para abrir a caixa de diálogo **de configuração SAML básica.**

    ![Configurar Sign-On único com página SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** complete os seguintes passos:

    ![Secção básica de configuração SAML](common/sp-identifier.png)

    a. Na **caixa de URL** sign in, introduza um URL que tenha este padrão: `https://<company>.answerhub.com`

    b. Na caixa **identifier (Entity ID),** insira um URL que tenha este padrão: `https://<company>.answerhub.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e o identificador de inscrição real. Contacte a [equipa de apoio do AnswerHub](mailto:success@answerhub.com) para obter os valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **descarregamento** ao lado **do Certificado (Base64)**, de acordo com os seus requisitos, e guarde o certificado no seu computador.

    ![Link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar o AnswerHub,** copie os URL ou URLs apropriados, com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

   Pode copiar estes URLs:
    - URL de Inicio de Sessão

    - Identificador de Azure Ad

    - Logout URL

### <a name="configure-answerhub-single-sign-on"></a>Configurar o answerHub único sign-on

Nesta secção, configurar um único sº de sção para o AnswerHub.  

**Para configurar o answerHub um único sinal de inscrição:**

1. Numa janela diferente do navegador, inscreva-se no site da empresa AnswerHub como administrador.

    > [!NOTE]
    > Se precisar de ajuda para configurar o AnswerHub, contacte a equipa de suporte do [AnswerHub](mailto:success@answerhub.com.).

2. Vá para a **Administração.**

3. No **separador Utilizador e Grupos,** no painel esquerdo, na secção **Definições Sociais,** selecione **Configuração SAML**.

4. No separador **IDP Config,** complete estes passos:

    ![O Screenshot mostra a página AnswerHub com o separador Grupos & dos Utilizadores selecionados.](./media/answerhub-tutorial/ic785172.png "Configuração SAML")  

    a. Na caixa de URL de login do **IDP,** cole o **URL de login** que copiou do portal Azure.

    b. Na caixa **URL do IDP Logout,** cole o **URL logout** que copiou do portal Azure.

    c. Na caixa **IDP Name Identifier Format,** insira o valor **identificador** selecionado na secção **Atributos** do Utilizador no portal Azure.

    d. Selecione **Chaves e Certificados**.

5. Na secção **Chaves e Certificados,** complete estes passos:

    ![Secção de Chaves e Certificados](./media/answerhub-tutorial/ic785173.png "Chaves e Certificados")  

    a. Abra o certificado codificado base64 que descarregou do portal Azure no Bloco de Notas, copie o seu conteúdo e, em seguida, cole o conteúdo na caixa **de Chave Pública IDP (formato x509).**

    b. Selecione **Guardar**.

6. No separador **IDP Config,** selecione **Guardar** novamente.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste chamado Britta Simon no portal Azure.

**Para criar um utilizador de teste AD Azure:**

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Selecione Azure Ative Directy, Utilizadores, Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do utilizador, complete estes passos.

    ![Propriedades do utilizador](common/user-properties.png)

    a. Na caixa **Nome,** **insira BrittaSimon**.

    b. Na caixa **de nome do utilizador,** **introduza brittasimon \@<a sua>de extensão.de>**.  
    Por exemplo, BrittaSimon@contoso.com.

    c. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, configura o utilizador Britta Simon para utilizar o Azure AD como único sinal de acesso, concedendo ao utilizador acesso ao AnswerHub.

**Para atribuir o utilizador de teste Azure AD:**

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações** e, em seguida, selecione **AnswerHub**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **AnswerHub**.

    ![Lista de candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Adicionar painel de atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista **de Utilizadores** e, em seguida, selecione o botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. 

7. Selecione o botão **Selecione** na parte inferior do ecrã.

8. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-an-answerhub-test-user"></a>Criar um utilizador de teste AnswerHub

Para que os utilizadores de Azure AD entrem no AnswerHub, tem de os adicionar no AnswerHub. No AnswerHub, esta tarefa é feita manualmente.

**Para criar uma conta de utilizador:**

1. Inscreva-se no site da empresa **AnswerHub** como administrador.

2. Vá para a **Administração.**

3. Selecione o **separador Grupos & Utilizadores.**

4. No painel esquerdo, na secção **Gerir utilizadores,** selecione **Criar ou importar utilizadores**, e, em seguida, selecione **Utilizadores & Grupos**.

    ![O Screenshot mostra a página AnswerHub com o separador Grupos & dos Utilizadores selecionados e o link criar ou importar utilizadores chamado.](./media/answerhub-tutorial/ic785175.png "Grupos & utilizadores")

5. Nas caixas apropriadas, insira o **endereço de e-mail,** nome de **utilizador** e **palavra-passe** de uma conta AD Azure válida que pretende adicionar e, em seguida, selecione **Guardar**.

> [!NOTE]
> Pode utilizar qualquer outra ferramenta de criação de conta de utilizador ou API fornecida pelo AnswerHub para configurar contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única do Azure AD utilizando o painel de acesso.

Quando selecionar o azulejo AnswerHub no painel de acesso, deverá ser automaticamente inscrito no AnswerHub para o qual configura sSO. Para obter mais informações sobre o painel de acesso, consulte [Introdução ao painel de acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar apps SaaS com Diretório Ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)