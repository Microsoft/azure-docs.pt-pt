---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Exium | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610235"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Tutorial: Azure Ative Directory integração única (SSO) com Exium

Neste tutorial, você vai aprender a integrar Exium com Azure Ative Directory (Azure AD). Quando integrar o Exium com a Ad Azure, pode:

* Controlo em Azure AD que tem acesso ao Exium.
* Permitir que os seus utilizadores sejam automaticamente inscritos no Exium com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inscrição única de exium (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Exium suporta SSO iniciado **SP.**

## <a name="adding-exium-from-the-gallery"></a>Adicionar Exium da galeria

Para configurar a integração do Exium no Ad Azure, precisa adicionar Exium da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Exium** na caixa de pesquisa.
1. Selecione **Exium** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Configure e teste Azure AD SSO para exium

Configure e teste Azure AD SSO com Exium utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Exium.

Para configurar e testar a Azure AD SSO com Exium, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Exium SSO](#configure-exium-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Exium test user](#create-exium-test-user)** - para ter uma contraparte de B.Simon em Exium que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Exium,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. Na caixa de texto **URL, digite** o URL: `https://service.exium.net/sign-in`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente Exium](mailto:support@exium.net) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Exium.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Exium**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-exium-sso"></a>Configurar Exium SsO

1. Inscreva-se no site da empresa Exium como administrador.

1. Na **Consola de Administração,** selecione **painel de perfil da empresa.**

    ![screenshot para consola de administração](./media/exium-tutorial/company-profile.png)

1. No **Perfil,** clique em **Definições SSO** e **edite-o.**

1. Execute os passos abaixo na secção **Definições SSO.**

    ![screenshot para Definições SSO](./media/exium-tutorial/update.png)

    a. Selecione **SSO Type** como **AzureAD** a partir do dropdown.

    b. Cole o valor do **url de metadados da Federação de Aplicações** no campo **URL de metadados DO SAML 2.0.**

    c. Copiar o valor **DE URL SSO SAML 2.0,** colar este valor na caixa de texto **URL de resposta** na secção **configuração básica do SAML** no portal Azure.

    d. Copiar o valor **de ID da entidade SAML 2.0 SP,** colar este valor na caixa de texto **do Identificador** na secção **configuração de SAML básico** no portal Azure.  

    e. Clique em **Update**.

### <a name="create-exium-test-user"></a>Criar utilizador de teste de Exium

1. Inscreva-se no site da empresa Exium como administrador.

1. Aceda à Gestão do **Utilizador -> Utilizadores** e clique em **Adicionar Utilizador**.

    ![screenshot para criar utilizador de teste](./media/exium-tutorial/add-user.png)

1. Introduza os campos necessários na página seguinte e clique em **Guardar**.

    ![screenshot para criar campos de utilizador de teste com botão de guardar](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de Exium, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do Exium e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo exium nas Minhas Apps, este será redirecionado para Exium Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Exium, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


