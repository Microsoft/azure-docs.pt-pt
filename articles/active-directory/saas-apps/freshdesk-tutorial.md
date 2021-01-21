---
title: 'Tutorial: Integração do Diretório Ativo Azure com | da FreshDesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: d14f69865c254becc8295034b6311b1641dfd498
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623726"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integração do Diretório Ativo Azure com o FreshDesk

Neste tutorial, você vai aprender a integrar o FreshDesk com o Azure Ative Directory (Azure AD). Quando integrar o FreshDesk com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao FreshDesk.
* Ative os seus utilizadores a serem automaticamente inscritos no FreshDesk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:
 
* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por FreshDesk (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* FreshDesk suporta SSO iniciado **pela SP**

## <a name="add-freshdesk-from-the-gallery"></a>Adicione FreshDesk da galeria

Para configurar a integração do FreshDesk no AD AZure, precisa de adicionar o FreshDesk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **FreshDesk** na caixa de pesquisa.
1. Selecione **FreshDesk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Configurar e testar Azure AD SSO para FreshDesk

Configure e teste Azure AD SSO com FreshDesk usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no FreshDesk.

Para configurar e testar o Azure AD SSO com o FreshDesk, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configure o FreshDesk SSO](#configure-freshdesk-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create FreshDesk test user](#create-freshdesk-test-user)** - para ter uma contraparte de Britta Simon em FreshDesk que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

1. No portal Azure, na página de integração de aplicações **FreshDesk,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **único sinal de configuração com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao FreshDesk.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **FreshDesk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-freshdesk-sso"></a>Configurar SSO freshdesk

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Freshdesk como administrador.

2. Selecione o **ícone de Segurança** e na secção **Segurança,** execute os seguintes passos:

    ![Início de Sessão Único](./media/freshdesk-tutorial/configure-1.png "Início de Sessão Único")
  
    a. Para **um único sinal ligado**, selecione **On**.

    b. No **Método de Início** de Sessão, selecione **SAML SSO**.

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

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de sinal de FreshDesk, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do FreshDesk e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo FreshDesk nas Minhas Apps, deverá ser automaticamente inscrito no FreshDesk para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o FreshDesk, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).