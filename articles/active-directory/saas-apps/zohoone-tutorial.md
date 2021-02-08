---
title: 'Tutorial: Integração do Diretório Ativo Azure com zoho One | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zoho One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 225d6b13c882566a6b71c5809d67955a27561ed6
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99821110"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Diretório Ativo Azure com o Zoho One

Neste tutorial, você vai aprender a integrar Zoho One com Azure Ative Directory (Azure AD). Quando integrar o Zoho One com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Zoho One.
* Ative os seus utilizadores a serem automaticamente inscritos no Zoho One com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Zoho One, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Zoho One única subscrição ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Zoho One apoia **SP** e **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-zoho-one-from-the-gallery"></a>Adicione Zoho One da galeria

Para configurar a integração do Zoho One em AD Azure, precisa adicionar o Zoho One da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Zoho One** na caixa de pesquisa.
1. Selecione **Zoho One** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Configure e teste Azure AD SSO para Zoho One

Configure e teste Azure AD SSO com Zoho One usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Zoho One.

Para configurar e testar Azure AD SSO com Zoho One, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure zoho One SSO](#configure-zoho-one-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Zoho One test user](#create-zoho-one-test-user)** - para ter uma contraparte de B.Simon in Zoho One que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Zoho One,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite o URL: `one.zoho.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > O valor **URL de resposta** anterior não é real. Você receberá o `<saml-identifier>` valor de #step4 da secção **Configure Zoho One Single Sign-On,** que é explicada mais tarde no tutorial.

    c. Clique **em Definir URLs adicionais**.

    d. Na caixa de texto **do Estado de Retransmissão,** digite o URL: `https://one.zoho.com`

5. Se desejar configurar a aplicação no modo iniciado pela **SP,** execute o seguinte passo:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > O valor **URL de inscrição** anterior não é real. Irá atualizar o valor com o URL de Sign-On real da secção **Configure Zoho One Single Sign-On,** que é explicada mais tarde no tutorial. 

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar Zoho One,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Zoho One.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Zoho One**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-zoho-one-sso"></a>Configure Zoho One SSO

1. Numa janela diferente do navegador web, inscreva-se no seu site da empresa Zoho One como administrador.

2. No separador **Organização,** clique em **Configuração** sob **autenticação SAML**.

    ![Zoho One org](./media/zoho-one-tutorial/set-up.png)

3. Na página Pop-up execute os seguintes passos:

    ![Zoho One sig](./media/zoho-one-tutorial/save.png)

    a. Na caixa de texto **URL de entrada,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Na caixa de texto **URL de assinatura,** cole o valor do **URL logout,** que copiou do portal Azure.

    c. Clique **em Procurar** para fazer o upload do Certificado **(Base64)** que descarregou a partir do portal Azure.

    d. Clique em **Guardar**.

4. Depois de guardar a configuração de autenticação SAML, copie o valor **DO IDENTIFICADOR SAML** e apique-o com o **URL de resposta** em vez `<saml-identifier>` de, como e colar o valor `https://accounts.zoho.com/samlresponse/one.zoho.com` gerado na caixa de texto URL **resposta** na secção **de Configuração SAML Básica.**

    ![Zoho One saml](./media/zoho-one-tutorial/saml-identifier.png)

5. Vá ao **separador Domínios** e, em seguida, clique em **Adicionar Domínio**.

    ![Domínio Zoho One](./media/zoho-one-tutorial/add-domain.png)

6. Na página **Add Domain,** execute os seguintes passos:

    ![Zoho One adicionar domínio](./media/zoho-one-tutorial/add-domain-name.png)

    a. Na caixa de texto **do Nome de Domínio,** escreva o domínio como contoso.com.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar o domínio siga [estes](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passos para verificar o seu domínio. Assim que o domínio for verificado, utilize o seu nome de domínio em **URL de acesso na** secção de **configuração SAML básica** no portal Azure.

### <a name="create-zoho-one-test-user"></a>Criar utilizador de teste Zoho One

Para permitir que os utilizadores de Azure AD inscrevam-se no Zoho One, devem ser a provisionados no Zoho One. No Zoho One, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Zoho One como administrador de segurança.

2. No **separador Utilizadores,** clique no **logótipo do utilizador.**

    ![Zoho Um utilizador](./media/zoho-one-tutorial/user.png)

3. Na página **'Adicionar Utilizador',** execute os seguintes passos:

    ![Zoho One adicionar utilizador](./media/zoho-one-tutorial/add-user.png)
    
    a. Na caixa de texto **Name,** insira o nome de utilizador como **Britta simon**.
    
    b. Na caixa de texto **do Endereço de E-mail,** insira o e-mail do utilizador como brittasimon@contoso.com .

    >[!Note]
    >Selecione o seu domínio verificado na lista de domínios.

    c. Clique em **Adicionar**.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Zoho One Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição zoho One e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Zoho One para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Zoho One nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Zoho One para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Zoho One, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
