---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com Azure AD SAML Toolkit / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Azure AD SAML Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675424"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Azure AD SAML Toolkit

Neste tutorial, você vai aprender a integrar O Azure AD SAML Toolkit com Azure Ative Directory (Azure AD). Quando integrar o Azure AD SAML Toolkit com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Azure AD SAML Toolkit.
* Ative os seus utilizadores a serem automaticamente inscritos no Azure AD SAML Toolkit com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Azure AD SAML Toolkit única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Azure AD SAML Toolkit suporta **SSO** iniciado sp

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Adicionar Azure AD SAML Toolkit da galeria

Para configurar a integração do Azure AD SAML Toolkit em Azure AD, é necessário adicionar o Azure AD SAML Toolkit da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar a partir da secção **de galeria,** **digite Azure AD SAML Toolkit** na caixa de pesquisa.
1. Selecione **Azure AD SAML Toolkit** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Configure e teste Azure AD SSO para Azure AD SAML Toolkit

Configure e teste Azure AD SSO com Azure AD SAML Toolkit usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Azure AD SAML Toolkit.

Para configurar e testar o Azure AD SSO com o Azure AD SAML Toolkit, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * Crie o utilizador de **[teste Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** - para ter uma contraparte de B.Simon em Azure AD SAML Toolkit que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Azure AD SAML,** encontre a secção **Gerir** e selecione **um único sinal de saúde** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** o URL: `https://samltoolkit.azurewebsites.net/`

    b. Na caixa de texto **URL de resposta,** digite o URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **Configurar Azure AD SAML Toolkit,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso ao Azure AD SAML Toolkit.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de aplicações, selecione **Azure AD SAML Toolkit** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Configurar a azure ad SAML Toolkit SSO

1. Abra uma nova janela do navegador web, se não tiver registado no site do Azure AD SAML Toolkit, primeiro registe-se clicando no **Registo** . Se já se registou, inscreva-se no site da empresa Azure AD SAML Toolkit utilizando o sinal registado em credenciais.

    ![Registo de kit de ferramentas Azure Ad SAML](./media/saml-toolkit-tutorial/register.png)

1. Clique na **Configuração SAML** .

    ![Configuração SAML de kit de ferramentas Azure Ad SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Clique em **Criar** .

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Na página **de Configuração SSO SAML,** execute os seguintes passos:

    ![Azure Ad SAML Toolkit cria configuração SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. Na caixa de texto **do URL de login,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    1. Na caixa de texto **Azure AD Identifier,** cole o valor do **identificador Azure AD,** que copiou do portal Azure.

    1. Na caixa de texto **URL logout,** cole o valor **URL logout,** que copiou a partir do portal Azure.

    1. Clique **em Escolher Ficheiro** e faça o upload do ficheiro Certificado **(Raw)** que descarregou a partir do portal Azure.

    1. Clique em **Criar** .

    1. Copiar valores de URL, IDENTIFIER e URL ACS na página de configuração SSO do SAML Toolkit e colar em caixas de texto respeitadas na **secção de Configuração BÁSICA SAML** no portal Azure.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Criar utilizador de teste de ferramentas Azure AD SAML

Nesta secção, um utilizador chamado B.Simon é criado no Azure AD SAML Toolkit. Por favor, crie um utilizador de teste na ferramenta registando um novo utilizador e fornecendo todos os detalhes do utilizador. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição do kit de ferramentas SAML, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição do KIT DE FERRAMENTAS SAML e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no telha SAML Toolkit no Painel de Acesso, deverá ser automaticamente inscrito no Kit de Ferramentas SAML para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado O Azure AD SAML Toolkit, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)