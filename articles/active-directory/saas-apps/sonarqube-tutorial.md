---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Sonarqube | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sonarqube.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: f756e738989775bf9c06b44a03f002c14f42e3ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182357"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Sonarqube

Neste tutorial, você vai aprender a integrar Sonarqube com Azure Ative Directory (Azure AD). Quando integrar o Sonarqube com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Sonarqube.
* Permita que os seus utilizadores sejam automaticamente inscritos na Sonarqube com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por sonarqube (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Sonarqube apoia SSO iniciado **pela SP**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-sonarqube-from-the-gallery"></a>Adicionando Sonarqube da galeria

Para configurar a integração da Sonarqube no AD Azure, é necessário adicionar o Sonarqube da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Sonarqube** na caixa de pesquisa.
1. Selecione **Sonarqube** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Configurar e testar Azure AD SSO para sonarqube

Configure e teste Azure AD SSO com Sonarqube usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Sonarqube.

Para configurar e testar a Azure AD SSO com a Sonarqube, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Sonarqube SSO](#configure-sonarqube-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Sonarqube test user](#create-sonarqube-test-user)** - para ter uma contraparte de B.Simon em Sonarqube que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Sonarqube,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:

    * **Para o Ambiente de Produção**

        `https://servicessonar.corp.microsoft.com/`

    * **Para Dev Ambiente**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração do Sonarqube,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso ao Sonarqube.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Sonarqube**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.

1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sonarqube-sso"></a>Configurar sonarqube SSO

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa Sonarqube como administrador.

1. Clique na **Configuração > de Administração > Segurança** e vá ao **Plugin SAML** executar os seguintes passos.

1. Copie os seguintes dados dos metadados IdP e cole-os nos campos de texto correspondentes no plugin SonarQube.
    1. IdP Entity ID
    2. URL de Inicio de Sessão
    3. Certificado X.509 
1. Guarde todos os detalhes.
    ![IDP de plugin saml](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. Na página **SAML,** execute os seguintes passos:

    ![Configuração do sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Alternar a opção **Ativada** para **sim**.

    b. Na caixa de texto **de identificação de aplicação,** insira o nome como **sonarqube**.

    c. Na caixa de texto **do Nome do Fornecedor,** insira o nome como **SAML**.

    d. Na caixa de texto **do Fornecedor ID,** cole o valor do **Identificador AD Azure,** que copiou do portal Azure.

    e. Na caixa **de texto url de login DA SAML,** cole o valor do URL de **login,** que copiou do portal Azure.

    f. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto do **certificado Fornecedor.**

    exemplo, Na **caixa de texto do utilizador SAML,** introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    h. Na **caixa de texto do nome de utilizador SAML,** introduza o valor `http://schemas.microsoft.com/identity/claims/displayname` .

    i. Na **caixa de texto do utilizador SAML,** introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    j. Clique em **Guardar**.

### <a name="create-sonarqube-test-user"></a>Criar utilizador de teste sonarqube

Nesta secção, cria-se um utilizador chamado B.Simon em Sonarqube. Trabalhe com a [equipa de suporte do Cliente Sonarqube](https://www.sonarsource.com/support/) para adicionar os utilizadores na plataforma Sonarqube. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de sonarqube onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição do Sonarqube e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo sonarqube no Painel de Acesso, este irá redirecionar para o URL de inscrição de Sonarqube. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

* Uma vez configurado o Sonarqube, pode impor controlos de sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).