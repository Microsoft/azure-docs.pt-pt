---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Egnyte | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 2662b686102a1a4f6aa6db0f7a4052de329def60
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519848"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Tutorial: Azure Ative Directory integração única (SSO) com Egnyte

Neste tutorial, você vai aprender a integrar Egnyte com Azure Ative Directory (Azure AD). Quando integrar egnyte com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Egnyte.
* Permita que os seus utilizadores sejam automaticamente inscritos na Egnyte com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Egnyte assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Egnyte suporta **SP** iniciado SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-egnyte-from-the-gallery"></a>Adicione Egnyte da galeria

Para configurar a integração da Egnyte no Azure AD, é necessário adicionar egnyte da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** digite **Egnyte** na caixa de pesquisa.
1. Selecione **Egnyte** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Configurar e testar Azure AD SSO para egnyte

Configure e teste Azure AD SSO com Form.com usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Form.com.

Para configurar e testar a Azure AD SSO com Form.com, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure egnyte SSO](#configure-egnyte-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Egnyte test user](#create-egnyte-test-user)** - para ter uma contraparte de B.Simon em Egnyte que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Egnyte,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.egnyte.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Estes valores não são reais. Atualizar o valor com o URL e URL de resposta de Sign-On reais. Contacte a [equipa de suporte do Cliente Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

5. Na secção **Configuração Egnyte,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Egnyte.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Egnyte**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-egnyte-sso&quot;></a>Configurar Egnyte SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Egnyte como administrador.

2. Clique em **Definições**.
   
    ![Definições 1](./media/egnyte-tutorial/settings-tab.png &quot;Definições")

3. No menu, clique em **Definições**.

    ![Menu 1](./media/egnyte-tutorial/menu-tab.png "Menu")

4. Clique no **separador Configuração** e, em seguida, clique em **Segurança**.

    ![Segurança](./media/egnyte-tutorial/configuration.png "Segurança")

5. Na secção **autenticação single Sign-On,** execute os seguintes passos:

    ![Único sinal na autenticação](./media/egnyte-tutorial/authentication.png "Único sinal na autenticação")   
    
    a. Como **autenticação única,** selecione **SAML 2.0**.
   
    b. Como **Fornecedor de Identidade,** selecione **AzureAD**.
   
    c. Colar **URL de Login** copiado do portal Azure para a caixa de texto **URL de login do fornecedor de identidade.**
   
    d. Colar **identificador Ad Azure** que copiou do portal Azure para a **caixa de texto iD da entidade fornecedora de identidade.**
      
    e. Abra o certificado codificado base-64 no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do **certificado de fornecedor de identidade.**
   
    f. Como **mapeamento padrão do utilizador**, selecione endereço de **e-mail**.
   
    exemplo, Como **Utilizar o valor do emitente específico do domínio,** selecione **desativado**.
   
    h. Clique em **Guardar**.

### <a name="create-egnyte-test-user"></a>Criar utilizador de teste Egnyte

Para permitir que os utilizadores da Azure AD inscrevam-se na Egnyte, devem ser a provisionados na Egnyte. No caso da Egnyte, o provisionamento é uma tarefa manual.

**Para a disponibilização de uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **Egnyte** como administrador.

2. Ir para **configurações \> Grupos & utilizadores**.

3. Clique **em Adicionar Novo Utilizador** e, em seguida, selecione o tipo de utilizador que pretende adicionar.
   
    ![Utilizadores](./media/egnyte-tutorial/add-user.png "Utilizadores")

4. Na secção **Novo Utilizador de Energia,** execute os seguintes passos:
    
    ![Novo Utilizador Padrão](./media/egnyte-tutorial/new-user.png "Novo Utilizador Padrão")   

    a. Na caixa de texto **por e-mail,** insira o e-mail de um utilizador como **o Brittasimon \@ contoso.com**.

    b. Na caixa de texto **username,** insira o nome de utilizador de um utilizador como **o Brittasimon**.

    c. Selecione **single Sign-On** como **tipo de autenticação**.
   
    d. Clique em **Guardar**.
    
    >[!NOTE]
    >O titular da conta Azure Ative Directory receberá um e-mail de notificação.
    >

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Egnyte ou APIs fornecidas pela Egnyte para fornecer contas de utilizador Azure AD.
>

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para egnyte Url de inscrição de sinal onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de Egnyte e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Egnyte nas Minhas Apps, este será redirecionado para URL de inscrição de Egnyte. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Egnyte, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
