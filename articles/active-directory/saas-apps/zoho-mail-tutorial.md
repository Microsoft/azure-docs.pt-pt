---
title: 'Tutorial: Integração do Diretório Ativo Azure com zoho | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648378"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integração do Diretório Ativo Azure com o Zoho

Neste tutorial, você vai aprender a integrar Zoho com Azure Ative Directory (Azure AD). Quando integrar o Zoho com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Zoho.
* Permita que os seus utilizadores sejam automaticamente inscritos no Zoho com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Zoho One, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Zoho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Zoho apoia **SP** iniciado SSO

## <a name="add-zoho-from-the-gallery"></a>Adicione Zoho da galeria

Para configurar a integração do Zoho no AD Azure, precisa adicionar zoho da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Zoho** na caixa de pesquisa.
1. Selecione **Zoho** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Configurar e testar Azure AD SSO para Zoho

Configure e teste Azure AD SSO com Zoho usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Zoho.

Para configurar e testar a Azure AD SSO com zoho, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Zoho SSO](#configure-zoho-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Zoho test user](#create-zoho-test-user)** - para ter uma contraparte de B.Simon em Zoho que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Zoho,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de apoio ao Cliente Zoho](https://www.zoho.com/mail/contact.html) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Zoho,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Zoho.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Zoho**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-zoho-sso"></a>Configure Zoho SSO

1. Numa janela diferente do navegador, inicie sessão no site da empresa Zoho Mail como administrador.

2. Vá ao **painel de controlo.**
   
    ![Painel de Controlo](./media/zoho-mail-tutorial/control-panel.png "Painel de Controlo")

3. Clique no separador **autenticação SAML.**
   
    ![Autenticação SAML](./media/zoho-mail-tutorial/saml-authentication.png "Autenticação SAML")

4. Na secção Detalhes de **Autenticação SAML,** execute os seguintes passos:
   
    ![Detalhes da autenticação SAML](./media/zoho-mail-tutorial/details.png "Detalhes da autenticação SAML")
   
    a. Na caixa de texto **do URL de login,** cole URL de **login** que copiou do portal Azure.
   
    b. Na caixa **de textos logout URL,** cole **URL logout** que copiou do portal Azure.
   
    c. Na caixa de texto **URL de alteração da palavra-passe,** cole URL de **palavra-passe** de alteração que copiou do portal Azure.
       
    d. Abra o certificado codificado base-64 descarregado do portal Azure no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a caixa de texto **PublicKey.**
   
    e. Como **algoritmo,** selecione **RSA**.
   
    f. Clique em **OK**.

### <a name="create-zoho-test-user"></a>Criar utilizador de teste Zoho

Para permitir que os utilizadores da Azure AD acedam ao Zoho Mail, devem ser ateados no Zoho Mail. No caso do Zoho Mail, o provisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Zoho Mail ou APIs fornecidas pelo Zoho Mail para fornecer contas de utilizadores Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para obter uma conta de utilizador, execute os seguintes passos:

1. Faça login no seu site da empresa **Zoho Mail** como administrador.

1. Vá ao **Control Panel Mail & \> Docs**.

1. Vá aos **detalhes do utilizador Adicionar \> Utilizador**.
   
    ![A screenshot mostra o site Zoho Mail com detalhes do utilizador e adicionar o utilizador selecionado.](./media/zoho-mail-tutorial/add-user-1.png "Adicionar Utilizador")

1. No diálogo **de utilizadores Adicionar,** execute os seguintes passos:
   
    ![A screenshot mostra a caixa de diálogo dos utilizadores Add onde pode introduzir os valores descritos.](./media/zoho-mail-tutorial/add-user-2.png "Adicionar Utilizador")
   
    a. Na caixa de texto **Name Name,** digite o primeiro nome de utilizador como **Britta**.

    b. Na caixa de texto **Do Último Nome,** digite o último nome de utilizador como **Simon**.

    c. Na caixa de texto **do Email ID,** digite o ID de e-mail do utilizador como **\@ brittasimon contoso.com**.

    d. Na caixa de texto **da palavra-passe,** introduza a palavra-passe do utilizador.
   
    e. Clique em **OK**.  
      
    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail com um link para confirmar a conta antes de ficar ativa.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para zoho Url de inscrição, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do Zoho e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Zoho nas Minhas Apps, deverá ser automaticamente inscrito no Zoho para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Zoho, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).