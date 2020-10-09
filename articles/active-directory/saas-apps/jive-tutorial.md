---
title: 'Tutorial: Integração do Azure Ative Directory com a Jive Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: da0e042664d6ca90c53b83bb38770a97d4e97498
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850758"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Tutorial: Azure Ative Directory integração única (SSO) com Jive

Neste tutorial, você vai aprender a integrar Jive com Azure Ative Directory (Azure AD). Quando integrar o Jive com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Jive.
* Ative os seus utilizadores a serem automaticamente inscritos no Jive com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Jive única inscrição (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Jive suporta **SSO** iniciado SP
* Jive suporta fornecimento [ **automatizado** de utilizadores](jive-provisioning-tutorial.md)
* Uma vez configurado o Jive, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-jive-from-the-gallery"></a>Adicionando Jive da galeria

Para configurar a integração do Jive no Azure AD, é necessário adicionar o Jive da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva Jive** na caixa de pesquisa.
1. Selecione **Jive** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-jive"></a>Configurar e testar Azure AD único sinal para Jive

Configure e teste Azure AD SSO com Jive usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Jive.

Para configurar e testar a Azure AD SSO com Jive, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Jive SSO](#configure-jive-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Jive test user](#create-jive-test-user)** - para ter uma contraparte de B.Simon em Jive que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Jive,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

   a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<instance name>.jivecustom.com`

   b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Jive Client](https://www.jivesoftware.com/services-support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração Jive,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Jive.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Jive**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-jive-sso"></a>Configurar Jive SsO

1. Para configurar um único sinal no lado **Jive,** inscreva-se no seu inquilino Jive como administrador.

1. No menu em cima, Clique em **SAML**.

    ![A screenshot mostra o separador SAML com o selecionado.](./media/jive-tutorial/tutorial_jive_002.png)

    a. Selecione **Ativado** no separador **Geral.**

    b. Clique no botão **SAVE ALL SAML SETTINGS.**

1. Navegue para o separador **METADADOS do IDP.**

    ![A screenshot mostra o separador SAML I D METADADOS selecionados.](./media/jive-tutorial/tutorial_jive_003.png)

    a. Copie o conteúdo do ficheiro XML de metadados descarregado e, em seguida, cole-o na caixa de texto **de Metadados do Fornecedor de Identidade (IDP).**

    b. Clique no botão **SAVE ALL SAML SETTINGS.**

1. Selecione **O SEPARADOR DE MAPEAMENTO DO ATRIBUTO DO UTILIZADOR.**

    ![A screenshot mostra o separador SAML com o USER ATTRIBUTE MAPPING selecionado.](./media/jive-tutorial/tutorial_jive_004.png)

    a. Na caixa de texto **por e-mail,** copie e cole o nome de atributo do valor do **correio.**

    b. Na caixa de texto **name First,** copie e cole o nome de atributo de valor **dado.**

    c. Na caixa de texto **do último nome,** copie e cole o nome de atributo do valor do **apelido.**

### <a name="create-jive-test-user"></a>Criar utilizador de teste Jive

O objetivo desta secção é criar um utilizador chamado Britta Simon em Jive. O Jive suporta o fornecimento automático do utilizador, que é por defeito ativado. Pode encontrar mais detalhes [aqui](jive-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

Se precisar de criar o utilizador manualmente, trabalhe com a [equipa de suporte do Jive Client](https://www.jivesoftware.com/services-support/) para adicionar os utilizadores na plataforma Jive.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Jive no Painel de Acesso, deverá ser automaticamente inscrito no Jive para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Jive com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Configurar o Provisionamento do Utilizador](jive-provisioning-tutorial.md)

- [Como proteger a Jive com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
