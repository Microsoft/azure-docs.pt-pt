---
title: 'Tutorial: Integração única de sign-on (SSO) do Azure Ative Directory com as desknets NEO | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o desknets NEO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580796"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o NEO da desknet

Neste tutorial, você vai aprender a integrar o NEO do desknet com O Diretório Ativo Azure (Azure AD). Quando integrar o NEO da desknet com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao NEO da desknet.
* Ativar os seus utilizadores a serem automaticamente inscritos no NEO da desknet com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* subscrição individual neo (SSO) ativada pela desknet.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* DESKNET NEO suporta **SP** iniciado SSO.

## <a name="adding-desknets-neo-from-the-gallery"></a>Adicionando o NEO do desknet da galeria

Para configurar a integração do NEO do desknet em AD Azure, você precisa adicionar o NEO do desknet da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva** o NEO da rede de trabalho na caixa de pesquisa.
1. Selecione **o NEO da desknet** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Configure e teste Azure AD SSO para o NEO da desknet

Configure e teste Azure AD SSO com o NEO da desknet usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no NEO do desknet.

Para configurar e testar o Azure AD SSO com o NEO da desknet, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o NEO SSO da desknet](#configure-desknets-neo-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador de teste NEO da desknet](#create-desknets-neo-test-user)** - para ter uma contraparte de B.Simon no NEO da desknet que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **NEO da rede de trabalho,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e sinal no URL. Contacte [a equipa de suporte do CLIENTE NEO da desknet](mailto:cloudsupport@desknets.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **NEO da rede configurada,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao NEO da desknet.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **o NEO da desknet**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-desknets-neo-sso"></a>Configure o NEO SSO da desknet

1. Inscreva-se no site da empresa NEO da sua rede como administrador.

1. No menu, clique no ícone **de definições de ligação de autenticação SAML.**

    ![Screenshot para definições de ligação de autenticação SAML.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. Nas **definições comuns,** clique na **utilização** da Colaboração de Autenticação SAML.

    ![Screenshot para utilização de autenticação SAML.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Execute os passos abaixo na secção **de definições de autenticação SAML.**

    ![Screenshot para a secção de definições de ligação de autenticação SAML.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. Na caixa de texto **do URL de acesso,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    b. Na caixa de texto de **ID da Entidade SP,** cole o valor **identificador,** que copiou a partir do portal Azure.

    c. Clique **em Escolher Ficheiro** para fazer o upload do ficheiro Certificado **(Base64)** descarregado do portal Azure para a caixa de texto **do certificado x.509.**

    d. Clique **em alterar**.

### <a name="create-desknets-neo-test-user"></a>Criar o utilizador de teste NEO da desknet

1. Inscreva-se no site da empresa NEO da sua rede como administrador.

1. No **menu,** clique no ícone **de definições de administrador.**

    ![Screenshot para definições de administrador.](./media/desknets-neo-tutorial/administrator-settings.png)

1. Clique no ícone **de definições** e selecione **a gestão** do utilizador nas **definições personalizadas**.

    ![Screenshot para definições de gestão do utilizador.](./media/desknets-neo-tutorial/user-management.png)

1. Clique **em Criar informações do utilizador.**

    ![Screenshot para o botão de informação do utilizador.](./media/desknets-neo-tutorial/create-new-user.png)

1. Preencha os campos necessários na página abaixo e clique na **criação**.

    ![Screenshot para a secção de criação de utilizador.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL NEO Sign-on da desknet, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL neo-on do desknet e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo NEO da desknet nas Minhas Apps, este será redirecionado para o URL NEO-on de desknet. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o NEO da desknet, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


