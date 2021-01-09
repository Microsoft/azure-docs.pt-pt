---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com WhosOffice Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o WhosOffice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 4e9c65b0a835f47565e79714d6f6b4ca6f47bdd3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047846"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosoffice"></a>Tutorial: Azure Ative Directory integração única (SSO) com WhosOffice

Neste tutorial, você vai aprender a integrar WhosOffice com Azure Ative Directory (Azure AD). Quando integrar o WhosOffice com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a WhosOffice.
* Permita que os seus utilizadores sejam automaticamente inscritos no WhosOffice com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por WhosOffice (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* WhosOffice apoia **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-whosoffice-from-the-gallery"></a>Adicionar WhosOffice da galeria

Para configurar a integração de WhosOffice em Azure AD, você precisa adicionar WhosOffice da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva WhosOffice** na caixa de pesquisa.
1. Selecione **WhosOffice** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-whosoffice"></a>Configurar e testar Azure AD SSO para WhosOffice

Configure e teste Azure AD SSO com WhosOffice usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em WhosOffice.

Para configurar e testar a Azure AD SSO com a WhosOffice, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o WhosOffice SSO](#configure-whosoffice-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create WhosOffice test user](#create-whosoffice-test-user)** - para ter uma contraparte de B.Simon em WhosOffice que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **whosOffice,** encontre a secção **Gerir** e selecione **um único sinal de saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.my.whosoffice.com/int/azure/consume.aspx`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.my.whosoffice.com/int/azure`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e URL de inscrição. Contacte a [equipa de suporte do Cliente WhosOffice](mailto:support@whosoffice.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar o WhosOffice,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a WhosOffice.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **WhosOffice**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-whosoffice-sso"></a>Configure WhosOffice SSO

1. Para automatizar a configuração dentro do WhosOffice, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar O WhosOffice** irá direcioná-lo para a aplicação WhosOffice. A partir daí, forneça as credenciais de administração para assinar no WhosOffice. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o WhosOffice manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa WhosOffice como administrador.

1. Clique em **Definições** e **selecione Empresa**.

    ![A Screenshot mostra a Empresa selecionada a partir de Definições.](./media/whosoffice-tutorial/configuration1.png)

1. Clique em **Apps/Integrações**.

    ![O Screenshot mostra Apps / Integrações selecionadas a partir das definições da Empresa.](./media/whosoffice-tutorial/configuration2.png)

1. Selecione **o Microsoft Azure** a partir do dropdown do fornecedor e clique no **Activate Login Provider**.

    ![O Screenshot mostra o Fornecedor de Login Ativado selecionado para o Microsoft Azure.](./media/whosoffice-tutorial/configuration3.png)

1. Faça o upload do ficheiro de metadados da federação descarregado a partir do portal Azure clicando na opção **Upload.**
    
    ![A screenshot mostra a opção upload para um ficheiro Meta Data.](./media/whosoffice-tutorial/configuration4.png)

### <a name="create-whosoffice-test-user"></a>Criar utilizador de testes whosOffice

1. Numa janela diferente do navegador web, inscreva-se no site da WhosOffice como administrador.

1. Clique em **Definições** e **selecione Utilizadores**.

    ![A screenshot mostra os utilizadores selecionados a partir de Definições.](./media/whosoffice-tutorial/user1.png)

1. Selecione **Criar novo utilizador**.

    ![Screenshot shows Criar novo Utilizador selecionado.](./media/whosoffice-tutorial/user2.png)

1. Forneça os detalhes necessários do utilizador de acordo com o seu requisito de organização.

    ![A Screenshot mostra a nova caixa de diálogo do Utilizador onde pode introduzir dados do utilizador.](./media/whosoffice-tutorial/user3.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O Sinal de WhosOffice no URL onde pode iniciar o fluxo de login.

* Vá diretamente ao URL de inscrição de WhosOffice e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no WhosOffice para o qual configura o SSO

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo whosOffice nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no WhosOffice para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado WhosOffice, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).