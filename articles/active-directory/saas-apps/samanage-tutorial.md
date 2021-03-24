---
title: 'Tutorial: Integração do Azure Ative Directory com o SolarWinds Service Desk (anteriormente Samanage) | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o SolarWinds Service Desk (anteriormente Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956048"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Tutorial: Integração do Azure Ative Directory com o SolarWinds Service Desk (anteriormente Samanage)

Neste tutorial, você vai aprender a integrar SolarWinds com Azure Ative Directory (Azure AD). Quando integrar o SolarWinds com AZure AD, pode:

* Controlo em Azure AD que tem acesso a SolarWinds.
* Capacitar os seus utilizadores a serem automaticamente inscritos no SolarWinds com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela SolarWinds (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SolarWinds suporta SSO iniciado **SP.**

## <a name="add-solarwinds-from-the-gallery"></a>Adicione SolarWinds da galeria

Para configurar a integração do SolarWinds no AD Azure, é necessário adicionar o SolarWinds da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** digite **SolarWinds** na caixa de pesquisa.
1. Selecione **SolarWinds** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Configurar e testar Azure AD SSO para SolarWinds

Configure e teste Azure AD SSO com SolarWinds usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em SolarWinds.

Para configurar e testar o Azure AD SSO com solarWinds, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure SolarWinds SSO](#configure-solarwinds-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create SolarWinds test user](#create-solarwinds-test-user)** - para ter uma contraparte de B.Simon em SolarWinds que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **SolarWinds,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL e o Identificador de Inscrição real, que é explicado mais tarde no tutorial. Para mais detalhes contacte [a equipa de suporte do Cliente Samanage.](https://www.samanage.com/support) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar SolarWinds,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a SolarWinds.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SolarWinds**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>Configure SolarWinds SSO

1. Numa janela diferente do navegador web, inicie sessão no site da empresa SolarWinds como administrador.

2. Clique **no Painel de Instrumentos** e selecione **Configurar** no painel de navegação esquerdo.
   
    ![Dashboard](./media/samanage-tutorial/tutorial-samanage-1.png "Dashboard")

3. Clique **em 'S sign-on' único**.
   
    ![Único sign-on](./media/samanage-tutorial/tutorial-samanage-2.png "Início de Sessão Único")

4. Navegue para iniciar sessão utilizando a secção **SAML,** execute os seguintes passos:
   
    ![Faça login usando SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Faça login usando SAML")
 
    a. Clique **em Ativar Sign-On simples com SAML**.  
 
    b. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.    
 
    c. Confirme que o URL de início de **sessão** corresponde ao **URL** da secção **de configuração BÁSICA SAML** no portal Azure.
 
    d. Na caixa de texto **URL logout,** insira o valor do **URL logout** que copiou do portal Azure.
 
    e. Na caixa de texto do **emitente SAML,** digite o id URI da aplicação definido no seu fornecedor de identidade.
 
    f. Abra o certificado codificado base-64 descarregado do portal Azure no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a Pasta do **Seu Fornecedor de Identidade x.509 Certificado abaixo** da caixa de texto.
 
    exemplo, Clique **em Criar utilizadores se não existirem em SolarWinds**.
 
    h. Clique em **Atualizar**.

### <a name="create-solarwinds-test-user"></a>Criar utilizador de teste SolarWinds

Para permitir que os utilizadores de Azure AD iniciem sessão no SolarWinds, devem ser aprovisionados em SolarWinds.  
No caso da SolarWinds, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu site da empresa SolarWinds como administrador.

2. Clique **no Painel de Instrumentos** e selecione **Configurar** na panela de navegação esquerda.
   
    ![Configuração](./media/samanage-tutorial/tutorial-samanage-1.png "Configuração")

3. Clique no **separador Utilizadores**
   
    ![Utilizadores](./media/samanage-tutorial/tutorial-samanage-6.png "Utilizadores")

4. Clique **em Novo Utilizador**.
   
    ![Novo Utilizador](./media/samanage-tutorial/tutorial-samanage-7.png "Novo Utilizador")

5. Digite o **Nome** e o Endereço de **E-mail** de uma conta Azure Ative Directory que pretende provisionar e clique em **Criar utilizador.**
   
    ![Criar Utilizador](./media/samanage-tutorial/tutorial-samanage-8.png "Criar Utilizador")
   
   >[!NOTE]
   >O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador solarWinds ou APIs fornecidas pela SolarWinds para fornecer contas de utilizadores do Azure Ative Directory.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de sinais de SolarWinds, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição solarWinds e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo SolarWinds nas Minhas Apps, este irá redirecionar para o URL de sinal de Sinais solar. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado SolarWinds, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).