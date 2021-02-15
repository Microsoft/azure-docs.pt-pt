---
title: 'Tutorial: Integração do Azure Ative Directory com | autotask Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Autotask Workplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 978acefa3044dcc44410b3f41aae62314a66b3c3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383310"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Tutorial: Integração do Azure Ative Directory com Autotask Workplace

Neste tutorial, você vai aprender a integrar o Autotask Workplace com Azure Ative Directory (Azure AD). Quando integrar o Autotask Workplace com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Autotask Workplace.
* Ative os seus utilizadores a serem automaticamente inscritos no Autotask Workplace com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por autotask Workplace (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Autotask Workplace suporta **SP e IDP** iniciado SSO

## <a name="add-autotask-workplace-from-the-gallery"></a>Adicione o Local de Trabalho automático da galeria

Para configurar a integração do Autotask Workplace em AD Azure, precisa adicionar o Autotask Workplace da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Autotask Workplace** na caixa de pesquisa.
1. Selecione **Autotask Workplace** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Configurar e testar Azure AD SSO para o Local de Trabalho autotasco

Configure e teste Azure AD SSO com Autotask Workplace usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Autotask Workplace.

Para configurar e testar a Azure AD SSO com o Autotask Workplace, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Autotask Workplace SSO](#configure-autotask-workplace-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Autotask Workplace test user](#create-autotask-workplace-test-user)** - para ter uma contraparte de B.Simon no Autotask Workplace que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Autotask Workplace,** encontre a secção **Gerir** e selecione **um único sinal de saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente do Local de Trabalho da Autotask](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção Configurar o **Local de Trabalho automático,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Autotask Workplace.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Autotask Workplace**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-autotask-workplace-sso"></a>Configurar O SSO do Local de Trabalho autotasco

1. Numa janela diferente do navegador web, faça login no Workplace Online usando as credenciais de administrador.

    > [!Note]
    > Ao configurar o IdP, será necessário especificar um subdomínio. Para confirmar o subdomínio correto, faça login no Workplace Online. Uma vez iniciado o login, tome nota para o subdomínio na URL. O subdomínio é a parte entre o "https://" e o ".awp.autotask.net/" e devemos ser nós, eu, ca ou au.

2. Vá à **Configuração**  >  **Single Sign-On** e execute os seguintes passos:

    ![Configuração de sign-on único de marcação automática](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. Selecione a opção **Ficheiro de Metadados XML** e, em seguida, carre fique no upload do **metudata XML** da Federação descarregado a partir do portal Azure.

    b. CLIQUE **EM ATIVAR SSO**.

    ![Configuração de aprovação única de assinatura automática](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. Selecione o eu confirmo que esta informação está correta e confio nesta caixa de verificação **IdP.**

    d. Clique **em APROVAÇÃO.**

> [!Note]
> Se necessitar de assistência para configurar o Autotask Workplace, consulte [esta página](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter assistência na sua conta workplace.

### <a name="create-autotask-workplace-test-user"></a>Criar utilizador de teste autotask

Nesta secção, cria-se um utilizador chamado Britta Simon no Autotask Workplace. Por favor, trabalhe com a [equipa de suporte do Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para adicionar os utilizadores na plataforma Autotask Workplace.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Autotask Workplace Sign no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição no local de trabalho automático e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Local de Trabalho Automático para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Local de trabalho de marca automática nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Local de Trabalho de Autotask para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Autotask Workplace, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
