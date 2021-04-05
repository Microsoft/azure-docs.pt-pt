---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com o Perímetro 81 | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Perimeter 81.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: cd6ba1da92a19a1f73fc67c0165bfb19b3bb77aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363854"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeter-81"></a>Tutorial: Azure Ative Directy integração única (SSO) com o Perímetro 81

Neste tutorial, você vai aprender a integrar o Perímetro 81 com O Diretório Ativo Azure (Azure AD). Quando integrar o Perímetro 81 com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Perímetro 81.
* Permita que os seus utilizadores sejam automaticamente inscritos no Perímetro 81 com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada pelo perímetro 81 (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Perímetro 81 suporta **SP e IDP** iniciado SSO
* O Perímetro 81 suporta o provisionamento do utilizador **Just In Time**

## <a name="adding-perimeter-81-from-the-gallery"></a>Adicionar perímetro 81 da galeria

Para configurar a integração do Perímetro 81 no AD Azure, é necessário adicionar o Perímetro 81 da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **o Perímetro 81** na caixa de busca.
1. Selecione **o Perímetro 81** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-perimeter-81"></a>Configure e teste Azure AD SSO para o perímetro 81

Configure e teste Azure AD SSO com o Perímetro 81 usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Perímetro 81.

Para configurar e testar a Azure AD SSO com o Perímetro 81, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Perímetro 81 SSO](#configure-perimeter-81-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Perimeter 81 test user](#create-perimeter-81-test-user)** - para ter uma contraparte de B.Simon no Perímetro 81 que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **do Perímetro 81,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um valor utilizando o seguinte padrão: `urn:auth0:perimeter81:<SUBDOMAIN>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://auth.perimeter81.com/login/callback?connection=<SUBDOMAIN>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.perimeter81.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [o Perímetro 81 Equipa de Apoio ao Cliente](mailto:support@perimeter81.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar o **Perímetro 81,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso ao Perímetro 81.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Perímetro 81**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-perimeter-81-sso"></a>Perímetro de configuração 81 SSO

1. Para automatizar a configuração dentro do Perímetro 81, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique no **Perímetro 81** da Configuração irá direcioná-lo para a aplicação Perimeter 81. A partir daí, forneça as credenciais de administração para assinar no Perímetro 81. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Perímetro 81 manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa Perimeter 81 como administrador.

4. Vá a **Definições** e clique em **Fornecedores de Identidade**.

    ![Perímetro 81 configurações](./media/perimeter-81-tutorial/settings.png)

5. Clique no botão **Adicionar Fornecedor.**

    ![Fornecedor de adicionar perímetro 81](./media/perimeter-81-tutorial/add-provider.png)

6. Selecione **OS Fornecedores de Identidade SAML 2.0** e clique no botão **Continuar.**

    ![Perímetro 81 adicionar fornecedor de identidade](./media/perimeter-81-tutorial/add-identity-provider.png)

7. Na secção **SAML 2.0 Fornecedores de Identidade,** execute os seguintes passos:

    ![Perímetro 81 a montar saml](./media/perimeter-81-tutorial/setting-up-saml.png)

    a. Na caixa de texto **do Sign In URL,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Na caixa de texto **Do Domínio Aliases, insira** o valor do seu pseudónimo de domínio.

    c. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado de Assinatura X509.**

    > [!NOTE]
    > Em alternativa, pode clicar no **Upload PEM/CERT File** para fazer o upload do **Certificado (Base64)** que descarregou a partir do portal Azure.
    
    d. Clique em **Concluído**.

### <a name="create-perimeter-81-test-user"></a>Criar o utilizador de teste do Perímetro 81

Nesta secção, um utilizador chamado Britta Simon é criado no Perímetro 81. O Perímetro 81 suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Perímetro 81, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Perímetro 81 Sinal no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao Url de inscrição do Perímetro 81 e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Perímetro 81 para o qual configura o SSO.

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Perímetro 81 nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Perímetro 81 para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Perímetro 81, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
