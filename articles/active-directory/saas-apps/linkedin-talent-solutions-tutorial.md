---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com LinkedIn Talent Solutions | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LinkedIn Talent Solutions.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727347"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>Tutorial: Azure Ative Directy integração única (SSO) com Soluções de Talento LinkedIn

Neste tutorial, você vai aprender a integrar LinkedIn Talent Solutions com Azure Ative Directory (Azure AD). Quando integra soluções de talento linkedIn com Azure AD, pode:

* Control em Azure AD que tem acesso a LinkedIn Talent Solutions.
* Capacitar os seus utilizadores a serem automaticamente inscritos no LinkedIn Talent Solutions com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Acesso ao Centro de Contas no seu painel de soluções de talento linkedIn

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* LinkedIn Talent Solutions apoia **SP e IDP** iniciado SSO
* LinkedIn Talent Solutions suporta **provisão de** utilizadores just in time


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>Adicionar Soluções de Talento LinkedIn da galeria

Para configurar a integração do LinkedIn Talent Solutions no AD Azure, é necessário adicionar soluções de talento linkedIn da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Soluções de Talento LinkedIn** na caixa de pesquisa.
1. Selecione **LinkedIn Talent Solutions** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>Configurar e testar Azure AD SSO para soluções de talento linkedIn

Configure e teste Azure AD SSO com LinkedIn Talent Solutions usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Soluções de Talento LinkedIn.

Para configurar e testar o Azure AD SSO com a LinkedIn Talent Solutions, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o LinkedIn Talent Solutions SSO](#configure-linkedin-talent-solutions-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador de teste do LinkedIn Talent Solutions](#create-linkedin-talent-solutions-test-user)** - para ter uma contrapartida de B.Simon em LinkedIn Talent Solutions que está ligada à representação AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de **aplicações LinkedIn Talent Solutions,** encontre a secção **Gerir** e selecione **um único sinal de solução.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![image1](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![image2](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não estiverem a ser automaticamente poluídos, preencha os valores manualmente de acordo com o seu requisito.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://www.linkedin.com/talent/`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Soluções de Talento LinkedIn,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a LinkedIn Talent Solutions.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **LinkedIn Talent Solutions**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-linkedin-talent-solutions-sso"></a>Configurar Soluções de Talento LinkedIn SSO

1. Inscreva-se no website linkedIn Talent Solutions como administrador.
1. Navegue para o **Centro de Contas.** 
1. Selecione o separador **Definições** da barra de navegação.

    ![página de definições](./media/linkedin-talent-solutions-tutorial/settings.png)

1. Expandir a secção **Sign-On (SSO)** única. 

1. Clique no botão **Descarregar** para descarregar o **ficheiro de metadados** ou clicar ou clicar aqui para carregar e copiar campos **individuais a partir do** link do formulário para revelar os dados de configuração.

    ![ dados de configuração](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. Execute os seguintes passos para copiar os campos individuais do formulário.

    ![ configuração com dados de entrada](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. Copiar valor **de ID da Entidade,** cole este valor na caixa de texto **Azure AD Identifier** na secção **Configuração Básica SAML** no portal Azure.

    b. Copie o valor **DO URL ACS,** cole este valor na caixa de texto **URL de resposta** na secção **configuração básica do SAML** no portal Azure.

    c. Copie o conteúdo da caixa de texto **DO Certificado SP X.509 (assinatura)** no bloco de notas e guarde-o no seu computador.

1. Clique no **ficheiro Upload XML** para carregar o ficheiro **XML do Metadados da Federação** que copiou a partir do portal Azure.

    ![ Carregar ficheiro XML](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>Criar utilizador de teste de Soluções de Talento LinkedIn

Nesta secção, um utilizador chamado Britta Simon é criado no LinkedIn Talent Solutions. O LinkedIn Talent Solutions suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no LinkedIn Talent Solutions, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para LinkedIn Talent Solutions Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login do LinkedIn Talent Solutions e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito nas Soluções de Talento linkedIn para as quais configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do LinkedIn Talent Solutions nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito nas Soluções de Talento linkedIn para as quais configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado LinkedIn Talent Solutions pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).