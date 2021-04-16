---
title: 'Tutorial: Integração do Diretório Ativo Azure com | workfront Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workfront.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: cebfca387f1a7dc34f70085966f5b6e8e212f953
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478477"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Tutorial: Integração do Diretório Ativo Azure com Workfront

Neste tutorial, você vai aprender a integrar workfront com Azure Ative Directory (Azure AD). Quando integrar a Workfront com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Workfront.
* Ative os seus utilizadores a serem automaticamente inscritos no Workfront com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Workfront, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de inscrição única na frente de trabalho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Workfront suporta SSO iniciado **SP.**

## <a name="add-workfront-from-the-gallery"></a>Adicione workfront da galeria

Para configurar a integração da Workfront em Azure AD, é necessário adicionar Workfront da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **Workfront** na caixa de pesquisa.
1. Selecione **Workfront** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Configure e teste Azure AD SSO para workfront

Configure e teste Azure AD SSO com Workfront usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Workfront.

Para configurar e testar a Azure AD SSO com workfront, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure workfront SSO](#configure-workfront-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Workfront test user](#create-workfront-test-user)** - para ter uma contraparte de B.Simon em Workfront que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Workfront,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.attask-ondemand.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Workfront](https://www.workfront.com/services-and-support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar workfront,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Workfront.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Workfront**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-workfront-sso"></a>Configurar workfront SSO

1. Inscreva-se no site da empresa workfront como administrador.

2. Aceda a **um único sinal na configuração**.

3. No **diálogo único de sign-on,** execute os seguintes passos
    
    ![Configurar Sign-On Individuais](./media/workfront-tutorial/single-sign-on.png)
   
    a. Como **Tipo**, selecione **SAML 2.0**.
   
    b. Selecione o **ID do fornecedor de serviços**.
   
    c. Cole o **URL de login** na caixa de texto URL do Portal do **Login.**
   
    d. Colar **URL logout** na caixa de texto **URL de sign-out.**
   
    e. Colar **Alterar URL de palavra-passe** na caixa de texto DO URL de alteração da **palavra-passe.**
   
    f. Clique em **Guardar**.

### <a name="create-workfront-test-user"></a>Criar utilizador de teste workfront

O objetivo desta secção é criar um utilizador chamado Britta Simon em Workfront.

**Para criar um utilizador chamado Britta Simon in Workfront, execute os seguintes passos:**

1. Inscreva-se no site da empresa Workfront como administrador.
 
2. No menu em cima, clique em **Pessoas.**
 
3. Clique **em Nova Pessoa.** 

4. No diálogo New Person, execute os seguintes passos:
   
    ![Criar um utilizador de teste workfront](./media/workfront-tutorial/add-person.png)
   
    a. Na caixa de texto **do primeiro nome,** escreva "Britta".
   
    b. Na caixa de texto **do último nome,** escreva "Simon".
   
    c. Na caixa de texto **do Endereço de E-mail,** escreva o endereço de e-mail de Britta Simon no Diretório Ativo Azure.
   
    d. Clique **em Adicionar Pessoa.**

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição na frente de trabalho, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição na frente de trabalho e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo workfront nas Minhas Apps, este será redirecionado para URL de sign-on workfront. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Workfront, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
