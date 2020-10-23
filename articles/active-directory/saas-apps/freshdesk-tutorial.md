---
title: 'Tutorial: Integração do Azure Ative Directory com o FreshDesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: f120554574f8dc7d0b97744af6dad447b404005b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92451568"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integração do Diretório Ativo Azure com o FreshDesk

Neste tutorial, aprende-se a integrar o FreshDesk com o Azure Ative Directory (Azure AD).
A integração da FreshDesk com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao FreshDesk.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no FreshDesk (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o FreshDesk, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por um único sinal de FreshDesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* FreshDesk suporta SSO iniciado **pela SP**
* Uma vez configurado o FreshDesk, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionando FreshDesk da galeria

Para configurar a integração do FreshDesk no AD AZure, precisa de adicionar o FreshDesk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **FreshDesk** na caixa de pesquisa.
1. Selecione **FreshDesk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Configurar e testar Azure AD SSO para FreshDesk

Configure e teste Azure AD SSO com FreshDesk usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no FreshDesk.

Para configurar e testar o Azure AD SSO com o FreshDesk, complete os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configure o Sign-On Único do FreshDesk](#configure-freshdesk-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
    1. **[Create FreshDesk test user](#create-freshdesk-test-user)** - para ter uma contraparte de Britta Simon em FreshDesk que está ligada à representação AD AD do utilizador.
1. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **FreshDesk,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **único sign-on de Configuração com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de sinais,** digite um URL utilizando o seguinte padrão: ou qualquer outro valor sugerido pela `https://<tenant-name>.freshdesk.com` Freshdesk.

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pela Freshdesk.
     
    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do Cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação FreshDesk espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos, enquanto **o Identificador Exclusivo do Utilizador** está mapeado com o nome **user.userprincipalname,** mas o FreshDesk espera que esta alegação seja mapeada com **o user.mail,** pelo que é necessário editar o mapeamento do atributo clicando no ícone Editar e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração Single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **'Configurar', 'Set-up FreshDesk',** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao FreshDesk.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **FreshDesk**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **FreshDesk**.

    ![O link FreshDesk na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-freshdesk-single-sign-on"></a>Configurar Sign-On single freshdesk

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Freshdesk como administrador.

2. Selecione o **ícone de Segurança** e na secção **Segurança,** execute os seguintes passos:

    ![Início de Sessão Único](./media/freshdesk-tutorial/configure-1.png "Início de Sessão Único")
  
    a. Para **um único sinal ligado**, selecione **On**.

    b. No **Método de Início**de Sessão, selecione **SAML SSO**.

    c. No ID da Entidade fornecido pela caixa de texto **IdP,** cole o valor ID da **Entidade,** que copiou do portal Azure.

    d. Na caixa de texto **URL SSO SAML,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    e. Nas **Opções de Assinatura**, selecione **Apenas Afirmações Assinadas** a partir do dropdown.

    f. Na caixa **de textos LOGOUT URL,** cole o valor **URL logout,** que copiou a partir do portal Azure.

    exemplo, Na caixa de texto **do Certificado de Segurança,** cole o valor **do Certificado (Base64),** que obteve anteriormente.
  
    h. Clique em **Guardar**.

## <a name="create-freshdesk-test-user"></a>Criar utilizador de teste FreshDesk

Para permitir que os utilizadores de Azure AD acedam ao FreshDesk, devem ser a provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **da Freshdesk.**

1. No menu à esquerda, clique em **Administração** e no separador **Definições Gerais,** clique em **Agentes**.
  
    ![Agentes](./media/freshdesk-tutorial/create-user-1.png "Agentes")

1. Clique **em Novo Agente**.

    ![Novo Agente](./media/freshdesk-tutorial/create-user-2.png "Novo Agente")

1. No diálogo informação do agente, introduza os campos necessários e clique no **Agente Criar**.

    ![Informação do Agente](./media/freshdesk-tutorial/create-user-3.png "Informação do Agente")

    >[!NOTE]
    >O titular da conta AZure AD receberá um e-mail que inclui um link para confirmar a conta antes de ser ativada.
    >
    >[!NOTE]
    >Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Freshdesk ou APIs fornecidas pela Freshdesk para fornecer contas de utilizador Azure AD à FreshDesk.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo FreshDesk no Painel de Acesso, deverá ser automaticamente inscrito no FreshDesk para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)