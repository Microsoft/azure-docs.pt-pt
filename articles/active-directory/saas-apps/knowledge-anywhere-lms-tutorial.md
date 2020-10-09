---
title: 'Tutorial: Integração do Azure Ative Directory com Knowledge Anywhere LMS Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Knowledge Anywhere LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: bc820eb282d08c248b588aa926eb3fd837fe51c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858137"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Tutorial: Integrar conhecimento em qualquer lugar LMS com Diretório Ativo Azure

Neste tutorial, você aprenderá a integrar Knowledge Anywhere LMS com Azure Ative Directory (Azure AD). Quando integra o Knowledge Anywhere LMS com AD AZure, pode:

* Controle em Azure AD que tem acesso a Knowledge Anywhere LMS.
* Ative os seus utilizadores a serem automaticamente inscritos no Knowledge Anywhere LMS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Knowledge Anywhere LMS single sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Knowledge Anywhere LMS suporta **SSO** iniciado SP e suporta o fornecimento do utilizador **Just In Time.**

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Adicionar Conhecimento em qualquer lugar LMS da galeria

Para configurar a integração do Knowledge Anywhere LMS em AD AZure, precisa de adicionar Knowledge Anywhere LMS da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Knowledge Anywhere LMS** na caixa de pesquisa.
1. Selecione **Knowledge Anywhere LMS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Knowledge Anywhere LMS usando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Knowledge Anywhere LMS.

Para configurar e testar O SSO Azure AD com Knowledge Anywhere LMS, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o conhecimento em qualquer lugar LMS](#configure-knowledge-anywhere-lms)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B. Simon.
4. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste de Conhecimento em Qualquer Lugar LMS](#create-knowledge-anywhere-lms-test-user)** para ter uma contraparte de B. Simon in Knowledge Anywhere LMS que esteja ligada à representação AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Do Conhecimento em Qualquer lugar,** encontre a secção **Gerir** e selecione **'Único sinal de sismo'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    1. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real, que é explicado mais tarde no tutorial.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize este valor com o URL de inscrição real. Contacte [a equipa de suporte do cliente LMS para](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Conhecimento em Qualquer Lugar LMS,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Configure conhecimento em qualquer lugar LMS

1. Para automatizar a configuração dentro do Knowledge Anywhere LMS, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Knowledge Anywhere LMS** irá direcioná-lo para a aplicação Knowledge Anywhere LMS. A partir daí, forneça as credenciais de administração para assinar no Knowledge Anywhere LMS. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Knowledge Anywhere LMS, abra uma nova janela do navegador web e inscreva-se no site da empresa Knowledge Anywhere LMS como administrador e execute os seguintes passos:

4. Selecione no **separador Site.**

    ![A imagem mostra o separador Site.](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Selecione no separador **Definições SAML.**

    ![A screenshot mostra a página 'Conhecimento' em qualquer lugar com definições SAML selecionadas.](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Clique no **Add New**.

    ![A screenshot mostra o botão Adicionar Novo nas Definições do Fornecedor de Serviços.](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Na página **'Configurações SAML's Add/Update,** execute os seguintes passos:

    ![A screenshot mostra a página de Definições SAML de Adição/Atualização onde pode escriminar as alterações descritas aqui.](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Insira o Nome IDP de acordo com a sua organização. Para ex:- `Azure` .

    b. Na caixa de texto **IDP Entity ID,** cole o valor **do identificador Azure AD** , que copiou do portal Azure.

    c. Na caixa de texto do URL do **IDP,** cole o valor URL do **Login,** que copiou do portal Azure.

    d. Abra o ficheiro de certificado descarregado do portal Azure para o bloco de notas, copie o conteúdo do certificado e cole-o na caixa de texto **Certificate.**

    e. Na caixa **de textos logout URL,** cole o valor **URL logout,** que copiou do portal Azure.

    f. Selecione **o Site Principal** a partir do dropdown para o **Domínio**.

    exemplo, Copie o valor **de ID da Entidade SP** e cole-o na caixa de texto **identifier** na secção **configuração de SAML básico** no portal Azure.

    h. Copie o valor **URL de resposta SP (ACS)** e cole-o na caixa de texto URL de **resposta** na secção **de Configuração SAML Básica** no portal Azure.

    i. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá a B. Simon usar o Azure single sign-on, concedendo acesso ao Knowledge Anywhere LMS.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Knowledge Anywhere LMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-knowledge-anywhere-lms-test-user"></a>Criar conhecimento em qualquer lugar utilizador de teste LMS

Nesta secção, um utilizador chamado B. Simon é criado em Knowledge Anywhere LMS. O Knowledge Anywhere LMS suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Knowledge Anywhere LMS, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo De conhecimento em qualquer lugar do LMS no Painel de Acesso, deverá ser automaticamente inscrito no Knowledge Anywhere LMS para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)