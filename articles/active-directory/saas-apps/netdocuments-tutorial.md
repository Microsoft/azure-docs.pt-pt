---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com NetDocuments / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o NetDocuments.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: 536fcab8cdfea4846cc1122fa56c939f525388f4
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938479"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Tutorial: Azure Ative Directory integração única (SSO) com NetDocuments

Neste tutorial, você vai aprender a integrar NetDocuments com Azure Ative Directory (Azure AD). Quando integrar os NetDocuments com Azure AD, pode:

* Controlo em Azure AD que tem acesso a NetDocuments.
* Capacitar os seus utilizadores a serem automaticamente inscritos no NetDocuments com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* NetDocuments subscrição única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* NetDocuments suporta **SSO** iniciado SP

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionar NetDocuments da galeria

Para configurar a integração do NetDocuments no AD Azure, é necessário adicionar NetDocuments da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite NetDocuments** na caixa de pesquisa.
1. Selecione **NetDocuments** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Configurar e testar Azure AD único sinal de inscrição para NetDocuments

Configure e teste Azure AD SSO com NetDocuments usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em NetDocuments.

Para configurar e testar o Azure AD SSO com NetDocuments, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o NetDocuments SSO](#configure-netdocuments-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create NetDocuments test user](#create-netdocuments-test-user)** - para ter uma contraparte de B.Simon em NetDocuments que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **NetDocuments,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e URL de resposta. O ID do repositório é um valor que começa com **CA,** seguido por um código de caracteres de 8 caracteres associado ao seu Repositório NetDocuments. Pode consultar o [documento de suporte de identidade federada NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) para obter mais informações. Em alternativa, pode contactar a [equipa de suporte do cliente NetDocuments](https://support.netdocuments.com/hc/) para obter estes valores se tiver dificuldades em configurar a utilização das informações acima. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação NetDocuments espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação NetDocuments espera que **o identificador** de identificação seja mapeado com **o employeeid** ou qualquer outra reclamação que seja aplicável à sua Organização como **identificador**de nomes, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar NetDocuments,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a NetDocuments.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **NetDocuments**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-netdocuments-sso"></a>Configurar NetDocuments SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa NetDocuments como administrador.

2. No canto superior direito, selecione o seu nome>**Administrador**.

3. Selecione **Centro de Segurança**.
   
    ![A screenshot mostra o Centro de Segurança selecionado a partir de Documentos Legais.](./media/netdocuments-tutorial/security-center.png "Centro de Segurança")

4. Selecione **Autenticação Avançada**.
    
    ![Configure opções avançadas de autenticação](./media/netdocuments-tutorial/advance-authentication.png "Configure opções avançadas de autenticação")

5.  No **separador De ID Federado,** execute os seguintes passos:   
   
    ![Identidade Federada](./media/netdocuments-tutorial/federated-id.png "Identidade federada")
   
    a. Como **tipo de servidor de identidade federado,** selecione Ative **Directory Federation Services**.
    
    b.  **Selecione Choose File**, para carregar o ficheiro de metadados descarregado que descarregou a partir do portal Azure.
    
    c.  Selecione **SAVE**.

### <a name="create-netdocuments-test-user"></a>Criar utilizador de teste NetDocuments

Para permitir que os utilizadores de Azure AD inscrevam-se no NetDocuments, devem ser a provisionados em NetDocuments. No caso dos NetDocuments, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **NetDocuments** como administrador.

2. No canto superior direito, selecione o seu nome>**Administrador**.
   
    ![Administração](./media/netdocuments-tutorial/user-admin.png "Administrador")

3. Selecione **Utilizadores e grupos**.
   
    ![A screenshot mostra grupos & de utilizadores selecionados a partir de documentos legais.](./media/netdocuments-tutorial/users-groups.png "Repositório")

4. Na caixa de texto **do Endereço de E-mail,** digite o endereço de e-mail de uma conta de Diretório Ativo Azure válida que pretende obter e, em seguida, clique em **Adicionar Utilizador**.
   
    ![Endereço de e-mail](./media/netdocuments-tutorial/user-mail.png "Endereço de E-mail")
   
    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail que inclui um link para confirmar a conta antes de ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador netDocuments ou APIs fornecidas pela NetDocuments para fornecer contas de utilizador do Azure Ative Directory.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo NetDocuments no Painel de Acesso, deverá ser automaticamente inscrito nos NetDocuments para os quais configura SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente NetDocuments com Azure AD](https://aad.portal.azure.com/)
