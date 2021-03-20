---
title: 'Tutorial: Integração do Diretório Ativo Azure com | snowflake Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Snowflake.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1af0209265ec4945950120e80a83e19c8ab2eb4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726285"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Tutorial: Integração do Diretório Ativo Azure com Floco de Neve

Neste tutorial, você vai aprender a integrar Snowflake com Azure Ative Directory (Azure AD). Quando integrar o Floco de Neve com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Snowflake.
* Permita que os seus utilizadores sejam automaticamente inscritos no Snowflake com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Floco de Neve, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por snowflake

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD num único sinal de teste.

- Floco de neve apoia **SP e IDP** iniciado SSO
- Floco de neve suporta [fornecimento e desprovisionamento automatizados de utilizadores](snowflake-provisioning-tutorial.md) (recomendado)

## <a name="adding-snowflake-from-the-gallery"></a>Adicionando floco de neve da galeria

Para configurar a integração do Snowflake em Azure AD, você precisa adicionar Snowflake da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Floco de Neve** na caixa de pesquisa.
1. Selecione **Snowflake** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Configure e teste Azure AD SSO para floco de neve

Configure e teste Azure AD SSO com Floco de Neve usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Snowflake.

Para configurar e testar o Azure AD SSO com floco de neve, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Snowflake SSO](#configure-snowflake-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Snowflake test user](#create-snowflake-test-user)** - para ter uma contraparte de B.Simon em Snowflake que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Snowflake,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos, se desejar configurar a aplicação no modo iniciado pelo **IDP:**

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado sp:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. Na caixa de texto **URL logout,** digite um URL utilizando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente Snowflake](https://support.snowflake.net/s/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar flocos de neve,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Snowflake.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Snowflake**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-snowflake-sso"></a>Configure Snowflake SSO

1. Numa janela diferente do navegador web, faça login em Snowflake como Administrador de Segurança.

1. **Altere a Função** para **ACCOUNTADMIN,** clicando no **perfil** no lado superior direito da página.

    > [!NOTE]
    > Isto é separado do contexto selecionado no canto superior direito sob o seu Nome de Utilizador.
    
    ![O administrador do Floco de Neve](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Abra o **certificado Base 64 descarregado** no bloco de notas. Copie o valor entre "-----BEGIN CERTIFICATE-----" e "-----END CERTIFICATE-----" e cole-o nas aspas ao lado do **certificado** abaixo. No **ssoUrl,** cole o valor URL do **Login** que copiou do portal Azure. Selecione **todas as consultas** e clique em **Executar**.

   ![Floco de neve](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>Criar utilizador de teste snowflake

Para permitir que os utilizadores de Azure AD entrem em Snowflake, devem ser a provisionados em Snowflake. Em Snowflake, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login em Snowflake como administrador de segurança.

2. **Altere a Função** para **ACCOUNTADMIN,** clicando no **perfil** no lado superior direito da página.  

    ![O administrador do Floco de Neve](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Crie o utilizador executando a consulta abaixo do SQL, certificando-se de que o "nome de login" está definido para o nome de utilizador Azure AD na folha de cálculo, como mostrado abaixo.

    ![O administrador snowflake](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Snowflake Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura snowflake e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Floco de Neve para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo snowflake nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Snowflake para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Snowflake, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)