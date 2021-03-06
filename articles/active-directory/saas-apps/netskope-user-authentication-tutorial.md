---
title: 'Tutorial: Integração única de sign-on (SSO) do Azure Ative Directory com a autenticação do utilizador netskope | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Autenticação do Utilizador netskope.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 78ef49740c3e74c4ad5a30838d9721969ca70b36
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517002"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Tutorial: Azure Ative Directory integração única (SSO) com autenticação do utilizador netskope

Neste tutorial, você vai aprender a integrar a Autenticação do Utilizador netskope com O Diretório Ativo Azure (Azure AD). Quando integrar a Autenticação do Utilizador netskope com Azure AD, pode:

* Controle em Azure AD que tem acesso à Autenticação do Utilizador netskope.
* Ativar os seus utilizadores a serem automaticamente inscritos na Autenticação do Utilizador netskope com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Netskope User Authentication single sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A Autenticação do Utilizador netskope suporta o SSO iniciado pelo **SP e IDP.**

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Adicione autenticação do utilizador netskope da galeria

Para configurar a integração da Autenticação do Utilizador netskope em Azure AD, é necessário adicionar a Autenticação do Utilizador netskope da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite a autenticação do utilizador netskope** na caixa de pesquisa.
1. Selecione a autenticação do **utilizador netskope** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-netskope-user-authentication"></a>Configurar e testar Azure AD SSO para autenticação do utilizador netskope

Configure e teste Azure AD SSO com autenticação do utilizador netskope usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Autenticação do Utilizador netskope.

Para configurar e testar o Azure AD SSO com a Autenticação do Utilizador netskope, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO de autenticação do utilizador netskope](#configure-netskope-user-authentication-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste de autenticação do utilizador netskope](#create-netskope-user-authentication-test-user)** - para ter uma contraparte de B.Simon na Autenticação do Utilizador netskope que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure na página de integração da **aplicação de autenticação do utilizador netskope,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<tenantname>.goskope.com/<customer entered string>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Obterá estes valores explicados mais tarde no tutorial.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Os valores de URL de inscrição não são reais. Atualizar o valor url de inscrição com o URL de inscrição real. Contacte a equipa de [suporte ao cliente de autenticação do utilizador netskope](mailto:support@netskope.com) para obter o valor de URL de inscrição. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configuração da **Autenticação do Utilizador netskope,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Autenticação do Utilizador netskope.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Netskope User Authentication**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-netskope-user-authentication-sso"></a>Configurar a autenticação do utilizador netskope SSO

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa netskope como administrador.

1. Clique no separador **Plataforma Ativa.**

    ![A screenshot mostra plataforma ativa selecionada a partir de Definições.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Desloque-se para **o PROXY FORWARD** e selecione **SAML**.

    ![A screenshot mostra SAML selecionado a partir da Plataforma Ativa.](./media/netskope-user-authentication-tutorial/configuration.png)

1. Na página **definições DE SAML,** execute os seguintes passos:

    ![A screenshot mostra configurações SAML onde pode introduzir os valores descritos.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Copie o valor **do ID da Entidade SAML** e cole-o na caixa de texto **identifier** na secção **de Configuração Básica SAML** no portal Azure.

    b. Copie o valor **URL DO SAML ACS** e cole-o na caixa de texto **URL de resposta** na secção **configuração básica do SAML** no portal Azure.

1. Clique **na CONTA ADD**.

    ![A screenshot mostra ADD ACCOUNT selecionada no painel SAML.](./media/netskope-user-authentication-tutorial/add-account.png)

1. Na página **'Adicionar Conta SAML',** execute os seguintes passos:

    ![A screenshot mostra adicionar conta SAML onde pode introduzir os valores descritos.](./media/netskope-user-authentication-tutorial/configure-settings.png)

    a. Na caixa de texto **NAME,** forneça o nome Azure AD.

    b. Na caixa de texto **do URL do IDP,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    c. Na caixa de texto **IDPENTITY ID,** cole o valor do **identificador Azure AD,** que copiou do portal Azure.

    d. Abra o ficheiro de metadados descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **IDP CERTIFICATE.**

    e. Clique **em SAVE**.

### <a name="create-netskope-user-authentication-test-user"></a>Criar utilizador de teste de autenticação do utilizador netskope

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa netskope como administrador.

1. Clique no **separador Definições** a partir do painel de navegação esquerdo.

    ![Screenshot mostra definição selecionada.](./media/netskope-user-authentication-tutorial/configuration-settings.png)

1. Clique no separador **Plataforma Ativa.**

    ![A screenshot mostra plataforma ativa selecionada a partir de Definições.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Clique **no separador Utilizadores.**

    ![A screenshot mostra utilizadores selecionados a partir da Plataforma Ativa.](./media/netskope-user-authentication-tutorial/add-user.png)

1. CLIQUE **EM UTILIZADORES DE ADD**.

    ![A screenshot mostra a caixa de diálogo do Utilizadores onde pode selecionar utilizadores ADD.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Introduza o endereço de e-mail do utilizador que pretende adicionar e clique em **ADD**.

    ![O Screenshot mostra adicionar utilizadores onde pode introduzir uma lista de utilizadores.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Autenticação do Utilizador netskope no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de autenticação do utilizador netskope e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Autenticação do Utilizador netskope para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo de autenticação do utilizador netskope nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Autenticação do Utilizador netskope para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurada a Autenticação do Utilizador netskope, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
