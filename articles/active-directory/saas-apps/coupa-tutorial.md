---
title: 'Tutorial: Integração do Diretório Ativo Azure com coupa | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d6a686b38c9b67ed8b1a7801c2a6ba95ef29558c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652998"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integração do Diretório Ativo Azure com Coupa

Neste tutorial, você vai aprender a integrar Coupa com Azure Ative Directory (Azure AD). Quando integrar coupa com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a Coupa.
* Ative os seus utilizadores a serem automaticamente inscritos na Coupa com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Coupa assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Coupa apoia **SP** iniciado SSO

## <a name="add-coupa-from-the-gallery"></a>Adicione Coupa da galeria

Para configurar a integração de Coupa em Azure AD, você precisa adicionar Coupa da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Coupa** na caixa de pesquisa.
1. Selecione **Coupa** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Configurar e testar Azure AD SSO para Coupa

Configure e teste Azure AD SSO com Coupa usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Coupa.

Para configurar e testar a Azure AD SSO com Coupa, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure coupa SSO](#configure-coupa-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Coupa test user](#create-coupa-test-user)** - para ter uma contraparte de B.Simon inCoupa que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Coupa,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize este valor com o URL Sign-On real. Contacte [a equipa de apoio ao Cliente Coupa](https://success.coupa.com/Support/Contact_Us?) para obter este valor.

    b. Na caixa **identifier,** digite o URL:

    | Ambiente  | URL |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Produção | `sso-prd1.coupahost.com`|
    | | |

    c. Na caixa de texto **URL de resposta,** digite o URL:

    | Ambiente | URL |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produção | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Coupa, copie** os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Coupa.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Coupa**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-coupa-sso"></a>Configure Coupa SSO

1. Inscreva-se no site da sua empresa Coupa como administrador.

2. Vá para **o Controlo de Segurança \> de Configuração**.

    ![Controlos de Segurança](./media/coupa-tutorial/setup.png "Controlos de Segurança")

3. Na secção **'Iniciar sessão' utilizando a secção de credenciais Coupa,** execute os seguintes passos:

    ![Metadados Coupa SP](./media/coupa-tutorial/login.png "Metadados Coupa SP")

    a. Selecione **Iniciar sessão utilizando o SAML**.

    b. Clique **em Procurar** para carregar os metadados descarregados a partir do portal Azure.

    c. Clique em **Guardar**.

### <a name="create-coupa-test-user"></a>Criar utilizador de teste coupa

Para permitir que os utilizadores da Azure AD acedam a Coupa, devem ser a provisionados em Coupa.  

* No caso de Coupa, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa **Coupa** como administrador.

2. No menu em cima, clique em **Configuração** e, em seguida, clique em **Utilizadores**.

    ![Utilizadores](./media/coupa-tutorial/user.png "Utilizadores")

3. Clique em **Criar**.

    ![Create Users](./media/coupa-tutorial/create.png "Criar Utilizadores") (Criar Utilizadores)

4. Na secção Criação de **Utilizador,** execute os seguintes passos:

    ![Detalhes do utilizador](./media/coupa-tutorial/details.png "Detalhes do utilizador")

    a. Digite o **Login**, **Nome Próprio**, **Apelido**, **ID Sign-On único,** **E-mail** atributos de uma conta de Diretório Ativo Azure válida que pretende apresentar nas caixas de texto relacionadas.

    b. Clique em **Criar**.

    >[!NOTE]
    >O titular da conta Azure Ative Directory receberá um e-mail com um link para confirmar a conta antes de entrar em funcionamento.
    >

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Coupa ou APIs fornecidas pela Coupa para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para coupa Url de entrada de assinatura, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de assinatura coupa e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo coupa nas Minhas Apps, deverá ser automaticamente inscrito no Coupa para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Coupa, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)