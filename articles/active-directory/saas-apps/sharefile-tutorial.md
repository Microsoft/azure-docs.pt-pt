---
title: 'Tutorial: Integração do Azure Ative Directory com o Citrix ShareFile Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/18/2020
ms.author: jeedes
ms.openlocfilehash: 59f8148cf6e1d705094e8a6c4ee267b6c77ab7da
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518420"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração do Azure Ative Directory com o Citrix ShareFile

Neste tutorial, aprende-se a integrar o Citrix ShareFile com o Azure Ative Directory (Azure AD).
A integração do Citrix ShareFile com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Citrix ShareFile.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Citrix ShareFile (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Citrix ShareFile, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Citrix ShareFile assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Citrix ShareFile suporta **SSO** iniciado SP
* Uma vez configurado citrix ShareFile, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionar Citrix ShareFile da galeria

Para configurar a integração do Citrix ShareFile no AD Azure, é necessário adicionar o Citrix ShareFile da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Citrix ShareFile** na caixa de pesquisa.
1. Selecione **Citrix ShareFile** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, configura e testa o Azure AD com citrix ShareFile com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Citrix ShareFile.

Para configurar e testar o Azure AD com citrix ShareFile, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure citrix ShareFile SSO](#configure-citrix-sharefile-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    * **[Create Citrix ShareFile test user](#create-citrix-sharefile-test-user)** - para ter uma contraparte de Britta Simon no Citrix ShareFile que está ligada à representação AD Ad Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Citrix ShareFile,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte do Cliente Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Citrix ShareFile,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Citrix ShareFile.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Citrix ShareFile**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-citrix-sharefile-sso"></a>Configure Citrix ShareFile SSO

1. Numa janela diferente do navegador web, inicie sessão no site da empresa **Citrix ShareFile** como administrador.

1. No menu do lado esquerdo, clique em **Definições De**  ->  **Início**  ->  **de Sessão de Segurança do**Ícone & Política de Segurança .
   
    ![Administração de Conta](./media/sharefile-tutorial/settings-security.png "Administração de Conta")

1. Na página de diálogo **de configuração de assinatura única/ SAML 2.0** em **Definições Básicas,** execute os seguintes passos:
   
    ![Início de sessão único](./media/sharefile-tutorial/saml-configuration.png "Início de sessão único")
   
    a. Selecione **SIM** no **Enable SAML**.

    b. Copie o valor **do Emitente ShareFile/Entity ID** e cole-o na caixa URL do **Identificador** na caixa de diálogo **de configuração SAML básica** no portal Azure.
    
    c. Na sua caixa de texto **IDP Emitente/ID de entidade,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    d. Clique em **Alterar** ao lado do campo **certificado X.509** e, em seguida, faça o upload do certificado que descarregou a partir do portal Azure.
    
    e. Na caixa de texto **URL de login,** cole o valor do URL de **login** que copiou do portal Azure.
    
    f. Na caixa **de textos LOGOUT URL,** cole o valor do **URL logout** que copiou do portal Azure.

5. Clique em **Guardar** no portal de gestão Citrix ShareFile.

## <a name="create-citrix-sharefile-test-user"></a>Criar utilizador de teste Citrix ShareFile

1. Faça login no seu inquilino **Citrix ShareFile.**

2. Clique **em Pessoas**  ->  **Gerir utilizadores em casa**Criar novos  ->  **utilizadores**Criar  ->  **funcionários**.
   
    ![Criar Empregado](./media/sharefile-tutorial/create-user.png "Criar Empregado")

3. Na secção **informações básicas,** execute abaixo dos passos:
   
    ![Informação Básica](./media/sharefile-tutorial/user-form.png "Informação Básica")
   
    a. Na caixa de texto **Name Name,** escreva **o primeiro nome** do utilizador como **Britta**.
   
    b.  Na caixa de texto **Do Último Nome,** escreva **o apelido** do utilizador como **Simon**.
   
    c. Na caixa de texto **do Endereço de E-mail,** digite o endereço de e-mail de Britta Simon como ** \@ contoso.com**.

4. Clique em **Adicionar Utilizador**.
  
    >[!NOTE]
    >O titular da conta AZure AD receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Citrix ShareFile ou APIs fornecidas pela Citrix ShareFile para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Citrix ShareFile no Painel de Acesso, deverá ser automaticamente inscrito no Citrix ShareFile para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)