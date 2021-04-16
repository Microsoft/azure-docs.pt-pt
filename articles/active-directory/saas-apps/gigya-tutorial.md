---
title: 'Tutorial: Integração do Azure Ative Directory com Gigya | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Gigya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6956b8ba2dcad0e67caac797a47e5308e649d042
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516072"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: Integração do Azure Ative Directory com Gigya

Neste tutorial, você vai aprender a integrar Gigya com Azure Ative Directory (Azure AD). Quando integrar gigya com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a Gigya.
* Ative os seus utilizadores a serem automaticamente inscritos na Gigya com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Gigya single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Gigya suporta sSO iniciado **SP.**

## <a name="add-gigya-from-the-gallery"></a>Adicione Gigya da galeria

Para configurar a integração da Gigya no Azure AD, precisa adicionar Gigya da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** escreva **Gigya** na caixa de pesquisa.
1. Selecione **Gigya** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-gigya"></a>Configurar e testar Azure AD SSO para Gigya

Configure e teste Azure AD SSO com Gigya usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Gigya.

Para configurar e testar a Azure AD SSO com Gigya, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Gigya SSO](#configure-gigya-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Gigya test user](#create-gigya-test-user)** - para ter uma contraparte de B.Simon em Gigya que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Gigya,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `http://<companyname>.gigya.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Gigya](https://developers.gigya.com/display/GD/Opening+A+Support+Incident) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Gigya,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Gigya.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Gigya**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-gigya-sso&quot;></a>Configurar Gigya SSO

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Gigya como administrador.

2. Vá ao **Início \> de Sessão SAML de Definições** e, em seguida, clique no botão **Adicionar.**
   
    ![SAML Login](./media/gigya-tutorial/login.png &quot;SAML Login")

3. Na secção **de Login SAML,** execute os seguintes passos:
   
    ![Configuração SAML](./media/gigya-tutorial/configuration.png "Configuração SAML")
   
    a. Na caixa de texto **'Nome',** digite um nome para a sua configuração.
   
    b. Na caixa de texto **emitente,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure. 
   
    c. Na caixa de texto **URL de serviço de Sign-On única,** cole o valor do URL de **login** que copiou do portal Azure.
   
    d. Na caixa de texto **do formato ID name,** cole o valor do **formato identificador** de nome que copiou do portal Azure.
   
    e. Abra o certificado codificado base-64 no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a caixa de texto **do Certificado X.509.**
   
    f. Clique **em Guardar Definições**.

## <a name="create-gigya-test-user"></a>Criar utilizador de teste Gigya

Para permitir que os utilizadores de Azure AD acedam a Gigya, eles devem ser a provisionados em Gigya. No caso da Gigya, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para a disponibilização de uma conta de utilizador, execute os seguintes passos:

1. Faça login no seu site da empresa **Gigya** como administrador.

2. Vá ao **Administrador \> Gerir utilizadores** e, em seguida, clique em **Convidar utilizadores**.
   
    ![Gerir utilizadores](./media/gigya-tutorial/users.png "Gerir Utilizadores")

3. No diálogo de utilizadores convidados, execute os seguintes passos:
   
    ![Convidar utilizadores](./media/gigya-tutorial/invite-user.png "Convidar utilizadores")
   
    a. Na caixa de texto **do Email,** digite o pseudónimo de e-mail de uma conta válida do Azure Ative Directory que pretende.
    
    b. Clique **em Convidar Utilizador.**
      
    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail que inclui um link para confirmar a conta antes de ficar ativa.
    > 

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Gigya Sign-on URL onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de assinatura gigya e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Gigya nas My Apps, isto irá redirecionar para Gigya Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Gigya, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
