---
title: 'Tutorial: Integração do Azure Ative Directory com a OpsGenie | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OpsGenie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2ab1f9ee6095dfc0f708ec33622aad6f70fcae65
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Tutorial: Azure Ative Directory integração única (SSO) com OpsGenie

Neste tutorial, você vai aprender a integrar OpsGenie com Azure Ative Directory (Azure AD). Quando integrar a OpsGenie com a Ad Azure, pode:

* Controlo em Azure AD que tem acesso à OpsGenie.
* Capacitar os seus utilizadores a serem automaticamente inscritos na OpsGenie com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pela OpsGenie (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* OpsGenie apoia **IDP** iniciado SSO

## <a name="adding-opsgenie-from-the-gallery"></a>Adicionar OpsGenie da galeria

Para configurar a integração da OpsGenie no AD Azure, precisa adicionar OpsGenie da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite OpsGenie** na caixa de pesquisa.
1. Selecione **OpsGenie** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Configurar e testar Azure AD SSO para OpsGenie

Configure e teste Azure AD SSO com OpsGenie usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na OpsGenie.

Para configurar e testar a Azure AD SSO com a OpsGenie, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure OpsGenie SSO](#configure-opsgenie-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create OpsGenie test user](#create-opsgenie-test-user)** - para ter uma contraparte de B.Simon em OpsGenie que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **OpsGenie,** encontre a secção **Gerir** e selecione **um único sinal de sessão**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real, que é explicado mais tarde neste tutorial.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

1. Na secção **Configuração OpsGenie,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à OpsGenie.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **OpsGenie**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-opsgenie-sso"></a>Configure OpsGenie SSO

1. Para automatizar a configuração dentro do OpsGenie, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar OpsGenie** irá direcioná-lo para a aplicação OpsGenie. A partir daí, forneça as credenciais de administração para assinar na OpsGenie. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o OpsGenie manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa OpsGenie como administrador.

2. Clique em **Definições** e, em seguida, clique no **sinal único no** separador .
   
    ![Sign-On Única OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Para ativar o SSO, **selecione Ativado**.
   
    ![Screenshot que mostra a caixa de verificação "Ativada" selecionada.](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Na secção **Fornecedor,** clique no separador **Azure Ative Directory.**
   
    ![Screenshot que mostra a secção "Fornecedor" com o separador "Azure Ative Directory" selecionado.](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na página de diálogo do Diretório Ativo Azure, execute os seguintes passos:
   
    ![Screenshot que mostra a secção "Single sign-on" com o toggle "Enable single sign-on", "S A M L 2.0 Endpoint" e "Metadados U R L".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Copie o valor **ID URI** da aplicação e cole-o na caixa de texto **Identifier (Entity ID)** na secção **de Configuração BÁSICA SAML** no portal Azure.

    a. Copie o valor **URL de resposta** e cole-o na caixa de texto URL de **resposta** na secção **configuração BÁSICA SAML** no portal Azure.

    a. Na caixa de texto **SAML 2.0 Endpoint,** cole o valor URL de **login** que copiou do portal Azure.
    
    b. No **Url metadados:** caixa de texto, cole o url **de metadados da Federação de Aplicações** que copiou do portal Azure.
    
    c. Para ativar o SSO, ligue o **alternador de inscrição único** ativado.

    d. Clique **em Aplicar as definições SSO**.

### <a name="create-opsgenie-test-user"></a>Criar utilizador de teste OpsGenie

O objetivo desta secção é criar um utilizador chamado B.Simon in OpsGenie. 

1. Numa janela do navegador web, inscreva-se no seu inquilino OpsGenie como administrador.

2. Navegue para a lista de Utilizadores clicando nos **Utilizadores** no painel esquerdo.
   
    ![Definições OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Clique em **Adicionar Utilizador**.

4. No diálogo **do Utilizador Adicionar,** execute os seguintes passos:
   
    ![Screenshot que mostra o diálogo "Adicionar utilizador" com as caixas de texto "Email" e "Nome Completo" realçadas, e o botão "Guardar" selecionado.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Na caixa de texto **de e-mail,** digite o endereço de e-mail de B.Simon endereçado no Diretório Ativo Azure.
   
    b. Na caixa de texto **'Nome Completo',** tipo **B.Simon**.
   
    c. Clique em **Guardar**. 

> [!NOTE]
> B.Simon recebe um e-mail com instruções para configurar o seu perfil.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no OpsGenie para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo OpsGenie nas Minhas Apps, deverá ser automaticamente inscrito no OpsGenie para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Próximos passos

* Uma vez configurado OpsGenie, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).