---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Samsung Knox e os Business Services | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Samsung Knox e os Business Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: e1cf12d676de84bc18a123fbdf05b1170725eda8
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072887"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Samsung Knox e os Business Services

Neste tutorial, você vai aprender a integrar a Samsung Knox e os Business Services com o Azure Ative Directory (Azure AD). Quando integrar a Samsung Knox e os Business Services com a Azure AD, pode:

* Controle em Azure AD que tem acesso à Samsung Knox e serviços empresariais.
* Permita que os seus utilizadores sejam automaticamente inscritos na Samsung Knox e nos Business Services com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Samsung Knox e pelos Business Services (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Samsung Knox e Business Services suportam SSO iniciado **pela SP**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Adicionar Samsung Knox e Serviços Empresariais da galeria

Para configurar a integração da Samsung Knox e dos Business Services no Azure AD, é necessário adicionar a Samsung Knox e os Business Services da galeria à sua lista de aplicações geridas pela SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** escreva **Samsung Knox e Business Services** na caixa de pesquisa.
1. Selecione **Samsung Knox e Business Services** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Configure e teste Azure AD SSO para a Samsung Knox e serviços empresariais

Configure e teste Azure AD SSO com a Samsung Knox e a Business Services utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Samsung Knox e nos Business Services.

Para configurar e testar a Azure AD SSO com a Samsung Knox e a Business Services, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Samsung Knox e a Business Services SSO](#configure-samsung-knox-and-business-services-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Samsung Knox and Business Services test user](#create-samsung-knox-and-business-services-test-user)** - para ter uma contrapartida de B.Simon na Samsung Knox e Serviços Empresariais que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **da Samsung Knox e business Services,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL, digite** o URL:  `https://www.samsungknox.com`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Samsung Knox e Serviços empresariais.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Samsung Knox e Business Services**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-samsung-knox-and-business-services-sso"></a>Configure a Samsung Knox e a Business Services SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Samsung Knox e Business Services como administrador.

1. Clique no **Avatar** no canto superior direito.

    ![Avatar Samsung Knox](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Na barra lateral esquerda, clique em **CONFIGURAÇÕES DE DIRETÓRIO ATIVO** e execute os seguintes passos.

    ![DEFINIÇÕES DE DIRETÓRIO ATIVO](./media/samsung-knox-and-business-services-tutorial/sso-settings.png)

    a. Na caixa de texto **identifier(entity ID),** cole o valor **identificador** que inseriu no portal Azure.

    b. Na caixa de texto **URL da federação de metadados** da App, cole o valor url da **Federação de Metadados** da App que copiou a partir do portal Azure.

    c. clique em **CONNECT TO AD SSO**.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Criar utilizador de teste samsung Knox e Business Services

Nesta secção, cria-se um utilizador chamado Britta Simon na Samsung Knox e business Services. Trabalhe com a [equipa de apoio da Samsung Knox e business Services](mailto:noreplyk.sec@samsung.com) para adicionar os utilizadores na plataforma Samsung Knox e Business Services. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de assinatura da Samsung Knox e serviços empresariais, onde pode iniciar o fluxo de login. 

* Vá diretamente à SAMSUNG Knox e aos Serviços empresariais URL e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da Samsung Knox e business Services nas My Apps, este será redirecionado para o URL de sign-on da Samsung Knox e dos Serviços Empresariais. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Samsung Knox e os Business Services, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


