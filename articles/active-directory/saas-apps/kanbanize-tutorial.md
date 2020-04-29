---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Kanbanize Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c5a3a096c5a44f681d23587837ae31fd1af33b2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72373218"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Tutorial: Azure Ative Directory integração individual (SSO) com kanbanize

Neste tutorial, você vai aprender a integrar Kanbanize com o Azure Ative Directory (Azure AD). Quando integrar o Kanbanize com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Kanbanize.
* Permita que os seus utilizadores sejam automaticamente inscritos na Kanbanize com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) de Kanbanize.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Kanbanize apoia **SP e IDP** iniciadoS SSO
* Kanbanize suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-kanbanize-from-the-gallery"></a>Adicionando Kanbanize da galeria

Para configurar a integração do Kanbanize em Azure AD, você precisa adicionar Kanbanize da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** escreva **Kanbanize** na caixa de pesquisa.
1. **Selecione Kanbanize** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Configure e teste Azure AD único sign-on para Kanbanize

Configure e teste Azure AD SSO com Kanbanize utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Kanbanize.

Para configurar e testar o Azure AD SSO com kanbanize, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure O SSO de Kanbanize](#configure-kanbanize-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Crie o utilizador de **[teste Kanbanize](#create-kanbanize-test-user)** - para ter uma contrapartida de B.Simon em Kanbanize que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Kanbanize,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

     a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.kanbanize.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.kanbanize.com/saml/acs`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **do Estado relé,** escreva um URL:`/ctrl_login/saml_login`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de apoio ao [Cliente Kanbanize](mailto:support@ms.kanbanize.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação Kanbanize espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de nomes é mapeado com **nome de utilizador.userprincipal .** A aplicação Kanbanize espera que o identificador de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone Editar e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Configurar Kanbanize',** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso a Kanbanize.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Kanbanize**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-kanbanize-sso"></a>Configure Kanbanize SSO

1. Para automatizar a configuração dentro do Kanbanize, é necessário instalar a extensão de navegador De sessão de **sinais de aplicações das Minhas Aplicações,** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Kanbanize** irá direcioná-lo para a aplicação Kanbanize. A partir daí, forneça as credenciais de administração para assinar em Kanbanize. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Kanbanize manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Kanbanize como administrador e execute os seguintes passos:

4. Vá para a direita superior da página, clique no logotipo **definições.**

    ![Configurações de Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Na página do painel da Administração do lado esquerdo do menu clique em **Integrações** e, em seguida, ativar **o Single Sign-On**.

    ![Integrações kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Na secção Integrações, clique em **CONFIGURE** para abrir a página **de integração de sinais únicos.**

    ![Kanbanize config](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Na página **de integração de sinais únicos** em **Configurações,** execute os seguintes passos:

    ![Integrações kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Na caixa de texto **ID id da Entidade ID,** colhe o valor do **Identificador Azure AD,** que copiou do portal Azure.

    b. Na caixa de texto **IDP Login Endpoint,** cola o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto **IDP Logout Endpoint,** cola o valor do URL de **Logout,** que copiou do portal Azure.

    d. No nome atributo para Caixa de Texto **de E-mail,** insira este valor`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. No **nome atributo para caixa** de texto de nome próprio, insira este valor`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. No **nome atributo para Caixa** de Texto apelido, insira este valor`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Pode obter estes valores combinando o espaço de nome e os valores de nome do respetivo atributo da secção de atributos do Utilizador no portal Azure.

    g. No Notepad, abra o certificado codificado base-64 que descarregou do portal Azure, copie o seu conteúdo (sem os marcadores de arranque e final), e depois cole-o na caixa **de certificadoid X.509.**

    h. Verifique **o login enable com o SSO e o Kanbanize**.

    i. Clique em **Guardar Definições**.

### <a name="create-kanbanize-test-user"></a>Criar o utilizador de teste Kanbanize

Nesta secção, um utilizador chamado B.Simon é criado em Kanbanize. A Kanbanize suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Kanbanize, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, contacte a equipa de suporte ao [Cliente Kanbanize](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Kanbanize no Painel de Acesso, deve ser automaticamente inscrito no Kanbanize para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Kanbanize com Azure AD](https://aad.portal.azure.com/)

