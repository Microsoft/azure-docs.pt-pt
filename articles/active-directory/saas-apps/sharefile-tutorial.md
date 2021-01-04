---
title: 'Tutorial: Integração do Azure Ative Directory com o Citrix ShareFile Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: d7cafdcbbf44f3f501d54c13d1b4549c446ed8c8
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724005"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração do Azure Ative Directory com o Citrix ShareFile

Neste tutorial, aprende-se a integrar o Citrix ShareFile com o Azure Ative Directory (Azure AD).
A integração do Citrix ShareFile com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Citrix ShareFile.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Citrix ShareFile (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Citrix ShareFile, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Citrix ShareFile assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Citrix ShareFile suporta **SSO** iniciado SP

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionar Citrix ShareFile da galeria

Para configurar a integração do Citrix ShareFile no AD Azure, é necessário adicionar o Citrix ShareFile da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Citrix ShareFile** na caixa de pesquisa.
1. Selecione **Citrix ShareFile** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Configurar e testar Azure AD SSO para Citrix ShareFile

Nesta secção, configura e testa o Azure AD com citrix ShareFile com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Citrix ShareFile.

Para configurar e testar o Azure AD com citrix ShareFile, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure citrix ShareFile SSO](#configure-citrix-sharefile-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Citrix ShareFile test user](#create-citrix-sharefile-test-user)** - para ter uma contraparte de Britta Simon no Citrix ShareFile que está ligada à representação AD Ad Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Citrix ShareFile,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte do Cliente Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Citrix ShareFile,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Citrix ShareFile.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Citrix ShareFile**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-citrix-sharefile-sso"></a>Configure Citrix ShareFile SSO

1. Para automatizar a configuração dentro do **Citrix ShareFile,** é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar o Citrix ShareFile** irá direcioná-lo para a aplicação Citrix ShareFile. A partir daí, forneça as credenciais de administração para assinar no Citrix ShareFile. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Citrix ShareFile manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa Citrix ShareFile como administrador.

1. No **painel de instrumentos**, clique em **Definições** e selecione **Definições de Administração**

    ![Administração](./media/sharefile-tutorial/settings.png)

1. Nas Definições de Administração, aceda **à**  ->  **Política de Segurança & Segurança**.
   
    ![Administração de Conta](./media/sharefile-tutorial/settings-security.png "Administração de Conta")

1. Na página de diálogo **de configuração de assinatura única/ SAML 2.0** em **Definições Básicas,** execute os seguintes passos:
   
    ![Início de sessão único](./media/sharefile-tutorial/saml-configuration.png "Início de sessão único")
   
    a. Selecione **SIM** no **Enable SAML**.

    b. Copie o valor **do Emitente ShareFile/Entity ID** e cole-o na caixa URL do **Identificador** na caixa de diálogo **de configuração SAML básica** no portal Azure.
    
    c. Na sua caixa de texto **IDP Emitente/ID de entidade,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    d. Clique em **Alterar** ao lado do campo **certificado X.509** e, em seguida, faça o upload do certificado que descarregou a partir do portal Azure.
    
    e. Na caixa de texto **URL de login,** cole o valor do URL de **login** que copiou do portal Azure.
    
    f. Na caixa **de textos LOGOUT URL,** cole o valor do **URL logout** que copiou do portal Azure.

5. Clique em **Guardar** no portal de gestão Citrix ShareFile.

## <a name="create-citrix-sharefile-test-user"></a>Criar utilizador de teste Citrix ShareFile

1. Faça login no seu inquilino **Citrix ShareFile.**

2. Clique **em Pessoas**  ->  **Gerir utilizadores em casa** Criar novos  ->  **utilizadores** Criar  ->  **funcionários**.
   
    ![Criar Empregado](./media/sharefile-tutorial/create-user.png "Criar Empregado")

3. Na secção **informações básicas,** execute abaixo dos passos:
   
    ![Informações Básicas](./media/sharefile-tutorial/user-form.png "Informações Básicas")
   
    a. Na caixa de texto **Name Name,** escreva **o primeiro nome** do utilizador como **Britta**.
   
    b.  Na caixa de texto **Do Último Nome,** escreva **o apelido** do utilizador como **Simon**.
   
    c. Na caixa de texto **do Endereço de E-mail,** digite o endereço de e-mail de Britta Simon como **\@ contoso.com**.

4. Clique em **Adicionar Utilizador**.
  
    >[!NOTE]
    >O titular da conta AZure AD receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Citrix ShareFile ou APIs fornecidas pela Citrix ShareFile para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para citrix ShareFile Url de entrada, onde pode iniciar o fluxo de login.

* Vá diretamente ao URL de inscrição do Citrix ShareFile e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Citrix ShareFile nas Minhas Apps, este será redirecionado para o URL de inscrição do Citrix ShareFile. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado citrix ShareFile, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).