---
title: 'Tutorial: Integração de Diretório Ativo Azure com Conhecimento Em Qualquer Lugar LMS [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e o Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44324bbdd5af6675dfb4f5664cbbde2627edfec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67098569"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Tutorial: Integrar o Conhecimento em qualquer lugar LMS com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o Knowledge Anywhere LMS com azure Ative Directory (Azure AD). Quando integrar o Knowledge Anywhere LMS com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Knowledge Anywhere LMS.
* Permita que os seus utilizadores sejam automaticamente inscritos no Knowledge Anywhere LMS com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Conhecimento Em qualquer lugar LMS única subscrição ativada por SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Conhecimento Em qualquer lugar LMS suporta **SP** iniciado SSO e suporta o fornecimento de utilizadores **Just In Time.**

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Adicionar Conhecimento em qualquer lugar LMS da galeria

Para configurar a integração do Knowledge Anywhere LMS em Azure AD, você precisa adicionar Knowledge Anywhere LMS da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Knowledge Anywhere LMS** na caixa de pesquisa.
1. Selecione **Knowledge Anywhere LMS** from results panel and then add the app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Conhecimento Em qualquer lugar LMS utilizando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Knowledge Anywhere LMS.

Para configurar e testar o Azure AD SSO com conhecimento em qualquer lugar LMS, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Conhecimento em qualquer lugar LMS](#configure-knowledge-anywhere-lms)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B. Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de AD Azure.
5. **[Crie conhecimento em qualquer lugar O utilizador do teste LMS](#create-knowledge-anywhere-lms-test-user)** para ter uma contrapartida de B. Simon em Saber Em Qualquer Lugar LMS que esteja ligado à representação do utilizador da AD Azure.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **LMS knowledge anywhere,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    1. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Identificação e Resposta real, o que é explicado mais tarde no tutorial.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > O valor do URL de inscrição não é real. Atualize este valor com o URL de início de sinal real. Contacte o [Knowledge Anywhere LMS Client support team](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar o **Conhecimento em qualquer lugar lMS,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Configure conhecimento em qualquer lugar LMS

1. Para automatizar a configuração dentro do Knowledge Anywhere LMS, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Conhecimento Em qualquer lugar LMS** irá direcioná-lo para a aplicação Knowledge Anywhere LMS. A partir daí, forneça as credenciais de administração para assinar no Knowledge Anywhere LMS. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar manualmente o Knowledge Anywhere LMS, abra uma nova janela do navegador web e instique-se no site da empresa Knowledge Anywhere LMS como administrador e execute os seguintes passos:

4. Selecione no separador **Site.**

    ![Conhecimento em qualquer lugar Configuração LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Selecione no **separador Definições SAML.**

    ![Conhecimento em qualquer lugar Configuração LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Clique no **Add New**.

    ![Conhecimento em qualquer lugar Configuração LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Na página **de Definições SAML Adicionar/Atualizar,** execute os seguintes passos:

    ![Conhecimento em qualquer lugar Configuração LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Insira o nome IDP de acordo com a sua organização. Para ex:- `Azure`.

    b. Na caixa de texto **ID entity ID,** a pasta **Azure AD Identifier** valor ,que copiou do portal Azure.

    c. Na caixa de texto **IDP URL,** cola o valor URL do **Login,** que copiou do portal Azure.

    d. Abra o ficheiro de certificado descarregado do portal Azure no bloco de notas, copie o conteúdo do certificado e cole-o em caixa de texto **certificado.**

    e. Na caixa de texto **logout URL,** cola o valor URL do **Logout,** que copiou do portal Azure.

    f. Selecione **Main Site** a partir do dropdown para o **Domínio**.

    g. Copie o valor de ID da **Entidade SP** e cole-o na caixa de texto **Identifier** na secção **de Configuração SAML Básica** no portal Azure.

    h. Copie o valor url de **URL sP Response (ACS)** e cole-o na caixa de texto **URL** resposta na secção **de configuração Básica SAML** no portal Azure.

    i. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B. Simon use o único sign-on Azure, concedendo acesso ao Knowledge Anywhere LMS.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Knowledge Anywhere LMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-knowledge-anywhere-lms-test-user"></a>Criar conhecimento em qualquer lugar LMS utilizador de teste

Nesta secção, um utilizador chamado B. Simon é criado em Knowledge Anywhere LMS. Conhecimento Em qualquer lugar LMS suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Knowledge Anywhere LMS, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo LMS Knowledge Anywhere no Painel de Acesso, deve ser automaticamente inscrito no Knowledge Anywhere LMS para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)